
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
