# Lab 4 — Partition, mirror, fail, recover

Pair: Dinmukhamed + Amira · Date: 11.11.2026 · Hypervisor: VirtualBox (Windows 11 and Ubuntu guests)

## Windows

Added one 1 GB dynamically allocated disk (`lab04-win.vdi`) to the existing SATA controller of the Windows 11 guest while it was powered off. In the guest (PowerShell as administrator), `Get-Disk` showed it as disk 1 with partition style `RAW`.

```powershell
Initialize-Disk -Number 1 -PartitionStyle GPT
New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter | Format-Volume -FileSystem NTFS -NewFileSystemLabel LAB04
```

```text
PS> Get-Disk | Select Number, FriendlyName, PartitionStyle, Size

Number FriendlyName  PartitionStyle        Size
------ ------------  --------------        ----
     0 VBOX HARDDISK GPT            68719476736
     1 VBOX HARDDISK GPT             1073741824



PS> Get-Volume | Where FileSystemLabel -eq 'LAB04'

DriveLetter FriendlyName FileSystemType DriveType HealthStatus OperationalStatus SizeRemaining       Size
----------- ------------ -------------- --------- ------------ ----------------- -------------       ----
E           LAB04        NTFS           Fixed     Healthy      OK                    989.98 MB 1007.93 MB
```

The volume is 1007.93 MB rather than 1 GB because GPT and NTFS keep part of the disk for their own structures.

**MBR or GPT:** GPT. The guest boots Windows 11 with UEFI, and its system disk (disk 0) is already GPT, so a GPT data disk keeps the machine consistent; GPT also stores a backup copy of the partition table at the end of the disk and protects it with a checksum, so a damaged table can be recovered, and it has no 2 TiB or four-primary-partition limit like MBR.

## Linux — layout before and after

Added three 1 GB dynamically allocated disks (`lab04-d1`, `lab04-d2`, `lab04-d3`) to the Ubuntu guest's SATA controller while it was powered off. They appear as `sdb`, `sdc`, `sdd`. The system disk is `sda`.

### Before

```text
$ lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
NAME                       SIZE TYPE FSTYPE      MOUNTPOINTS
loop0                      1.5M loop squashfs    /snap/hwctl/123
loop1                     66.8M loop squashfs    /snap/core24/1643
loop2                     50.1M loop squashfs    /snap/snapd/27710
loop3                      1.5M loop squashfs    /snap/hwctl/131
sda                         25G disk
├─sda1                       1G part vfat        /boot/efi
├─sda2                       2G part ext4        /boot
└─sda3                    21.9G part LVM2_member
  └─ubuntu--vg-ubuntu--lv   11G lvm  ext4        /
sdb                          1G disk
sdc                          1G disk
sdd                          1G disk
sr0                       1024M rom
```

### After

```text
$ lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
NAME                       SIZE TYPE  FSTYPE            MOUNTPOINTS
loop0                      1.5M loop  squashfs          /snap/hwctl/123
loop1                     66.8M loop  squashfs          /snap/core24/1643
loop2                     50.1M loop  squashfs          /snap/snapd/27710
loop3                      1.5M loop  squashfs          /snap/hwctl/131
sda                         25G disk
├─sda1                       1G part  vfat              /boot/efi
├─sda2                       2G part  ext4              /boot
└─sda3                    21.9G part  LVM2_member
  └─ubuntu--vg-ubuntu--lv   11G lvm   ext4              /
sdb                          1G disk
└─sdb1                    1022M part  linux_raid_member
  └─md0                   1021M raid1 ext4              /mnt/mirror
sdc                          1G disk
└─sdc1                    1022M part  linux_raid_member
sdd                          1G disk
└─sdd1                    1022M part  linux_raid_member
  └─md0                   1021M raid1 ext4              /mnt/mirror
sr0                       1024M rom
```

`md0` now runs on `sdb1` and `sdd1`. `sdc1` still carries its RAID superblock (so `lsblk` still labels it `linux_raid_member`), but it is no longer part of `md0`.

## What I did

All commands run in the Ubuntu guest from `~/lab04-storage`; each evidence file was saved at the moment that state existed.

**1. Look before touching, save the empty layout**

```bash
mkdir -p ~/lab04-storage/evidence && cd ~/lab04-storage
{ echo '$ lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS'; lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS; } > evidence/lsblk-before.txt
```

**2. One RAID partition on each disk**

```bash
for d in sdb sdc sdd; do sudo parted -s /dev/$d mklabel gpt mkpart primary 1MiB 100% set 1 raid on; done
```

**3. Create the mirror from two partitions, wait for `[2/2] [UU]`, save the healthy state**

```bash
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
cat /proc/mdstat
{ echo '$ cat /proc/mdstat'; cat /proc/mdstat; echo; echo '$ sudo mdadm --detail /dev/md0'; sudo mdadm --detail /dev/md0; } > evidence/array-healthy.txt
```

**4. File system, mount, proof files, checksum before the failure**

```bash
sudo mkfs.ext4 -L mirror /dev/md0
sudo mkdir -p /mnt/mirror
sudo mount /dev/md0 /mnt/mirror
sudo chown $USER: /mnt/mirror
echo "Lab 4 proof file — Dinmukhamed Muratbek — $(date)" > /mnt/mirror/proof.txt
head -c 20M /dev/urandom > /mnt/mirror/random.bin
cd /mnt/mirror && { echo '$ sha256sum proof.txt random.bin'; sha256sum proof.txt random.bin; } > ~/lab04-storage/evidence/checksum-before.txt; cd ~/lab04-storage
```

**5. Fail one disk on purpose (with `mdadm --fail`, not by detaching the disk), save the degraded state, prove the data survived**

```bash
sudo mdadm /dev/md0 --fail /dev/sdc1
sudo mdadm /dev/md0 --remove /dev/sdc1
{ echo '$ cat /proc/mdstat'; cat /proc/mdstat; echo; echo '$ sudo mdadm --detail /dev/md0'; sudo mdadm --detail /dev/md0; } > evidence/array-degraded.txt
cd /mnt/mirror && { echo '$ sha256sum proof.txt random.bin'; sha256sum proof.txt random.bin; } > ~/lab04-storage/evidence/checksum-after-failure.txt; cd ~/lab04-storage
diff <(tail -n +2 evidence/checksum-before.txt) <(tail -n +2 evidence/checksum-after-failure.txt) && echo "checksums match"
```

Output of the last line: `checksums match`.

**6. Add the third disk, rebuild, save the rebuilt state, keep the array after reboot**

```bash
sudo mdadm /dev/md0 --add /dev/sdd1
cat /proc/mdstat
{ echo '$ cat /proc/mdstat'; cat /proc/mdstat; echo; echo '$ sudo mdadm --detail /dev/md0'; sudo mdadm --detail /dev/md0; } > evidence/array-rebuilt.txt
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u
```

The rebuild of a 1 GB array with a write-intent bitmap finished before the next `cat /proc/mdstat`, so no `recovery = …%` line was caught.

**7. Evidence to the host** (run on the host, not in the guest)

```bash
scp -P 2222 -r dima@127.0.0.1:lab04-storage/evidence labs/lab04-storage/
```

## Three states

| State | What /proc/mdstat showed | File |
| --- | --- | --- |
| Healthy | `[2/2] [UU]` — sdb1 + sdc1, `State : clean` | [evidence/array-healthy.txt](evidence/array-healthy.txt) |
| Degraded | `[2/1] [U_]` — sdb1 only, `State : clean, degraded` | [evidence/array-degraded.txt](evidence/array-degraded.txt) |
| Rebuilt | `[2/2] [UU]` — sdd1 replaced sdc1, `State : clean` | [evidence/array-rebuilt.txt](evidence/array-rebuilt.txt) |

Checksums before and after the failure: **match**.

| File | SHA-256 before ([checksum-before.txt](evidence/checksum-before.txt)) | SHA-256 after ([checksum-after-failure.txt](evidence/checksum-after-failure.txt)) |
| --- | --- | --- |
| proof.txt | `14025afe…68d5fb` | `14025afe…68d5fb` |
| random.bin | `65b122fe…b11104` | `65b122fe…b11104` |

## RAID is not a backup

Your colleague deleted the wrong file from the mirror an hour ago. Does RAID 1 help? Why?

No, RAID 1 does not help. The mirror copies every write to both disks at the same moment, so the deletion happened on `sdb1` and `sdd1` together, and there is no older copy anywhere in the array to go back to. RAID protects against a disk failing, as in this lab, while only a backup (a separate copy from an earlier point in time, stored apart from the array) can bring back a file that a person deleted or overwrote.

## What did not work the first time

1. **The checksums did not match.** On the first attempt I ran the step that creates `proof.txt` and `random.bin` again after taking `checksum-before.txt`. Each run writes a new date and new random bytes, so the "before" and "after" sums came from different files, and the evidence showed a mismatch even though no data was lost. Fix: I tore the array down and repeated the whole experiment, creating the files exactly once.
2. **`lsblk-before.txt` was taken too late.** It already showed the partitions and `md0`, not three empty disks. Fix: before the second attempt I stopped the array (`mdadm --stop`), erased the RAID superblocks (`mdadm --zero-superblock`) and partition tables (`wipefs -a`), removed the old array from `/etc/mdadm/mdadm.conf`, rebooted, and only then saved `lsblk-before.txt`.
3. **Erasing the partition table did not erase the data.** On the second attempt `mkfs.ext4` warned that `/dev/md0` already contained an ext4 file system labelled `mirror` from the first attempt. `wipefs` only removed the signatures at the start of the disks; the new partitions and array started at the same offsets, so the old file system was still there. I answered `y` to overwrite it.
4. **Pasting a multi-line loop failed.** The terminal inserted `^[[200~` (bracketed paste) before the `for` loop, so bash saw a syntax error and the next line ran with an empty `$d`, as `parted /dev/`. It failed safely. Fix: `bind 'set enable-bracketed-paste off'` and pasting the loop as a single line.
5. **The Windows VM would not start** (`VERR_FILE_NOT_FOUND` for `lab04-win.vdi`). The disk file had been moved to the host's trash. Fix: restored it from `~/.local/share/Trash/files/` to the VM folder.

## Evidence


lsblk-before.txt — shows three empty 1 GB disks before partitioning.

array-healthy.txt — proves the RAID 1 array was healthy with [UU] and two active devices.

array-degraded.txt — proves one disk failed and the array continued working in degraded mode [U_].

checksum-before.txt — records SHA-256 checksums before disk failure.

checksum-after-failure.txt — proves the checksums remained identical after disk failure.

array-rebuilt.txt — proves the third disk was added and the array returned to [UU].
