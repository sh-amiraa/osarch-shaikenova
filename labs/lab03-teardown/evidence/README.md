# Lab 3 — Teardown, and the same machine from the command line

Pair: Dinmukhamed , Amira · Date: 05.11.2026

## Part 1 — Ten parts
### AMD Ryzen 5 7600
![](evidence/photos/ryzen5-7600.png)
<caption: what it is · connects to · limit>  — Source: <maker's link>
(… one block per part …)

## Two builds
### Build 1
| Part | Model | Why it fits here |
### Build 2
| Part | Model | Why it fits here |
What each build still needs: …

## Part 2 — Same facts, three operating systems
| Fact | Windows (PowerShell) — VirtualBox VM | Linux — host |
|---|---|---|
| CPU model | `Get-CimInstance Win32_Processor \| Select Name`<br>- AMD Ryzen 7 5800H with Radeon Graphics | `lscpu` (Model name)<br>- AMD Ryzen 7 5800H with Radeon Graphics |
| Cores / threads | `Get-CimInstance Win32_Processor \| Select NumberOfCores, NumberOfLogicalProcessors, MaxClockSpeed`<br>- 2 cores / 2 threads, 3194 MHz | `lscpu` (Core(s) per socket × Thread(s) per core)<br>- 8 cores / 16 threads, max 4465 MHz |
| Total memory | `Get-CimInstance Win32_ComputerSystem \| Select TotalPhysicalMemory`<br>- _TODO_ | `free -h`<br>- 14 Gi usable (2 × 8 GiB installed) |
| Memory modules and speed | `Get-CimInstance Win32_PhysicalMemory \| Select Manufacturer, PartNumber, Capacity, Speed`<br>- _TODO_ | `sudo dmidecode -t memory`<br>- 2 × 8 GiB Hynix DDR4 SODIMM (HMA81GS6DJR8N-XN), 3200 MT/s, channels A + B |
| Disk model and type | `Get-PhysicalDisk \| Select FriendlyName, MediaType, BusType, Size`<br>- VBOX HARDDISK, SATA, MediaType Unspecified, 64 GiB | `lsblk -d -o NAME,MODEL,ROTA,TRAN,SIZE`<br>- Samsung MZVLB512HBJQ, NVMe, ROTA 0 (SSD), 476.9 G |
| Free space | `Get-Volume` (SizeRemaining)<br>- C: 29.72 GB free of 62.92 GB | `df -h /`<br>- 72 G free of 451 G (84 % used) |
| Architecture (x86-64 / ARM64) | `$env:PROCESSOR_ARCHITECTURE`<br>- AMD64 | `uname -m`<br>- x86_64 |

### Summary
 
The two systems name the same things differently: Linux reports `x86_64` where Windows reports `AMD64` for the same 64-bit architecture, Linux uses "Thread(s) per core" and `ROTA 0` (non-rotational = SSD) where Windows uses `NumberOfLogicalProcessors` and `MediaType`, and Linux prints sizes in GiB while `Get-PhysicalDisk` returns raw bytes. The Windows VM sees the real CPU model but only the 2 virtual cores it was given and the 3194 MHz base clock, and instead of the host's Samsung NVMe SSD it sees a virtual 64 GB `VBOX HARDDISK` on an emulated SATA controller.
 
### Notes
 
- 16 GB of RAM is installed, but `free -h` shows 14 Gi because part of it is reserved for the integrated Radeon graphics.
- `lscpu` output was captured in a Russian locale (e.g. `Имя модели` = Model name, `Ядер на сокет` = Core(s) per socket). Run `LC_ALL=C lscpu` for English labels.
- `dmidecode` needs `sudo`.


## What did not work the first time
**`lscpu` printed its labels in Russian.**
 
- **What I saw:** the field names came out as `Архитектура`, `Имя модели`, `Ядер на сокет`, `Сокетов` instead of `Architecture`, `Model name`, `Core(s) per socket`, `Socket(s)`. Some lines stayed in English (`CPU(s)`, `Thread(s) per core`), so the output was a mix of both languages. `df -h` headers were also in Russian (`Файловая система`, `Размер`, `Дост`).
- **What it was:** not an error, just the system locale. `lscpu` and `df` translate their labels using the current `LANG`, which is set to Russian on this machine. This makes the output hard to compare with the English field names in the assignment and in Windows, and it breaks any `grep "Model name"`.
- **What I did:** reran the commands with the locale forced to English for that one command only:
```bash
  LC_ALL=C lscpu
  LC_ALL=C df -h /
```
 
  Now the labels match the documentation, e.g. `Model name: AMD Ryzen 7 5800H with Radeon Graphics`. The system language stays unchanged.

## Evidence
- [evidence/linux-specs.txt](evidence/linux-specs.txt) — 
- [evidence/windows-specs.txt](evidence/windows-specs.txt) — 
