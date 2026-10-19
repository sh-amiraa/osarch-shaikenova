
# Lab 0 — Inventory your own machine

Pair: Dinmukhamed and Amira                    Driver first half:
Machine: OMEN by HP Laptop Arch Linux
Date: 07.10.2026

## What I did

Steps, with commands in code blocks. Not prose about the steps.

sudo dmidecode -t bios[cite: 1]
ls /sys/firmware/efi[cite: 1]
cat /proc/cpuinfo | grep 'model name' | head -n 1[cite: 2]
nproc[cite: 2]
grep 'core id' /proc/cpuinfo | sort -u | wc -l[cite: 2]
lsblk -o NAME,MODEL,SIZE,TYPE,FSTYPE,MOUNTPOINTS[cite: 3]
lsblk -d -o NAME,MODEL,ROTA,TRAN,SIZE[cite: 3]
df -h[cite: 3]
sudo dmidecode --type 17 | awk '/Size:/ {size=$2} /Speed:/ {speed=$2" "$3; if(size !~ "No") print "Модуль: " size ", Скорость: " speed}'[cite: 4]
free -h[cite: 4]
grep -cE 'vmx|svm' /proc/cpuinfo[cite: 5]

## Result
BIOS / UEFI:
The BIOS vendor is AMI, version F.12 dated 12/09/2021.
SMBIOS 3.3.0 specification is supported.
The currently installed language is en|US|iso8859-1.
The /sys/firmware/efi directory contains config_table, efivars, esrt, fw_platform_size, fw_vendor, runtime, runtime-map, and systab.
CPU:
The system is equipped with an AMD Ryzen 7 5800H with Radeon Graphics.
There are 8 physical cores and 16 logical processors (threads).
Hardware virtualization is supported, with 16 vmx|svm entries found in /proc/cpuinfo.
RAM:
The system has 8 GB memory modules installed running at 3200 MT/s.
Total available RAM (Mem) is 14 GiB, with 2.7 GiB free and 8.5 GiB available.
The swap space is 15 GiB, with 0 bytes used.
Storage Subsystem:
The installed NVMe drive is a SAMSUNG MZVLB512HBJQ-000H1 with a capacity of 476.9 GB.
The drive is partitioned into three parts: nvme0n1p1 (2 GB, vfat, mounted at /boot), nvme0n1p2 (458.9 GB, ext4, mounted at /), and nvme0n1p3 (16 GB, swap).
On the root partition /, 311 GB (73%) is used, leaving 118 GB free. On the /boot partition, 188 MB (10%) is used, leaving 1.9 GB free.

## What did not work the first time

All worked correctly

## Evidence

| Step      | Evidence |
| ----------- | ----------- |
| Find the CPU model, core count and thread count.     | [Evidence](CPU.txt)       |
| Find the total memory, the number of installed modules and their speed.   | [Evidence](memory.txt)        |
| Find the disk model, its type (HDD, SATA SSD, NVMe) and the free space on the volume you will store VMs on.   | [Evidence](disk.txt)        |
| Find the firmware type (BIOS or UEFI) and its version and date.   | [Evidence](Bios.txt)        |
| Find out whether hardware virtualization is present and enabled. If it is not, this is the moment to fix it — you need it next week.   | [Evidence](Virt.txt)        |
