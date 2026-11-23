# Lab 6 — Live processes and memory pressure

Pair: Dinmukhamed + Amira · Date: 22.11.2026 · Guests: Ubuntu 26.04.1 LTS (1.6 GiB RAM and 2.0 GiB swap as reported by `free -h`) / Windows 11 Enterprise Evaluation, build 26100 (RAM: 4096 MB)

Times in Part 2 are the Ubuntu guest clock (UTC); Almaty is UTC+5.

## Part 1 — One process, three tools

Process chosen: **VBoxService** (`VBoxService.exe` on Windows, `/usr/sbin/VBoxService` on Ubuntu) — why: it is the VirtualBox Guest Additions service, it runs permanently on both guests, so the same program can be looked at in Task Manager, Process Explorer and htop without starting anything ourselves.

Evidence: `evidence/task-manager.png`, `evidence/process-explorer.png`, `evidence/htop.png`.

### Comparison table

| | Task Manager | Process Explorer | htop / ps |
|---|---|---|---|
| PID | 1888 | 1888 | 2089 |
| Parent (name and PID) | — (not shown) | services.exe (PID 876) | systemd (PID 1) — PPID column in htop = 1 |
| "Memory" as shown by default | 884 K ("Memory (private working set)") | 9,412 K (Working Set) | 3816 K (RES) |
| Private memory | 884 K (private working set); Commit size 2,464 K | Private Bytes 2,476 K | no column; roughly RES − SHR = 3816 − 3400 = 416 K |
| Working set / RES | 9,412 K (Working set (memory)) | 9,412 K (Working Set) | RES 3816 K (SHR 3400 K) |
| Virtual size | — (not shown; closest is Commit size 2,464 K) | 2,151,781,088 K (Virtual Size) | VIRT 349M (ps vsz: 357636) |
| Handles / file descriptors | 200 | 202 | Not displayed in htop; checked via /proc/2089/fd |
| Threads | 14 | 15 | 9 |

Other htop values for the same row: PRI 20, NI 0, state S, CPU% 0.0, MEM% 0.2, user root.

Note: Task Manager and Process Explorer show the same process (VBoxService.exe, PID 1888, parent services.exe 876). The live counters differ slightly (handles 200 vs 202, threads 14 vs 15) because the two tools sampled the process at slightly different moments; the memory figure they both report as working set is identical (9,412 K).

Command-line output (Ubuntu, same process, PID 2089):

```
ticket1@ticket1:~$ ps -o pid,ppid,user,rss,vsz,nlwp,cmd -p 2089
    PID    PPID USER       RSS    VSZ NLWP CMD
   2089       1 root      3816 357636    9 /usr/sbin/VBoxService

ticket1@ticket1:~$ ls/proc/2089/fd | wc -l
-bash: ls/proc/2089/fd: No such file or directory
0

ticket1@ticket1:~$ sudo ls -l /proc/2089/fd | head
[sudo: authenticate] Password:
total 0
lrwx------ 1 root root 64 Nov 22 17:06 0 -> /dev/null
lrwx------ 1 root root 64 Nov 22 17:06 1 -> /dev/null
lrwx------ 1 root root 64 Nov 22 17:06 2 -> /dev/null
lrwx------ 1 root root 64 Nov 22 17:06 3 -> /dev/vboxguest
lrwx------+1 root root 64 Nov 22 17:06 4 -> socket:[18955]
```
Note: Process 2089 was no longer active when this check was run (and `ls/proc/2089/fd` missed a space after `ls`), so `/proc/2089/fd` could not be listed and `wc -l` returned `0`.

### Where the tools disagree

1. **Three different "memory" numbers for the same program.** Task Manager's default "Memory" column is the *private working set* (884 K): only the private pages that are in RAM right now. Process Explorer's `Working Set` and Task Manager's `Working set (memory)` report the same number (9,412 K) and also count shared pages such as DLLs, so they are about ten times bigger. htop's `RES` (3816 K) is the Linux working-set analogue and also includes shared pages (`SHR` 3400 K). So the numbers are different measurements, not a mistake in one of the tools.
2. **Commit size ≈ Private Bytes.** Task Manager calls it *Commit size* (2,464 K), Process Explorer calls the same idea *Private Bytes* (2,476 K): memory the process has committed, whether or not it is in RAM at this moment. htop has no column for it.
3. **Virtual size is not memory in use.** Process Explorer shows 2,151,781,088 K (about 2 TB) for VBoxService.exe, and nearly every other process shows almost the same figure — it is reserved address space, not RAM. In the same way htop's `VIRT` (349M) is far larger than `RES` (3816 K).
4. **Parent process.** Task Manager does not show the parent at all; Process Explorer shows it in the tree and on the Image tab; htop shows it via the PPID column and tree view (F5).
5. **Different names for the same thing.** Windows *handles* vs Linux *file descriptors*; Windows *Threads* vs `NLWP` in `ps`.

## Part 2 — Memory pressure

Guest RAM: 1.6 GiB total · swap: 2.0 GiB total (from `free -h`)
Snapshot `before-lab6` taken before the experiment. Evidence: `evidence/memory-pressure.txt`.

Steps (commands, in order):

```
free -h
swapon --show
cd ~ && mkdir -p lab06 && cd lab06
echo "=== BEFORE $(date) ===" > memory-pressure.txt
free -h >> memory-pressure.txt
while true; do echo "=== $(date +%T) ==="; free -h; sleep 3; done | tee -a memory-pressure.txt     # 2nd terminal, left running
stress-ng --vm 2 --vm-bytes 70% --timeout 60s
stress-ng --vm 2 --vm-bytes 90% --timeout 60s
stress-ng --vm 2 --vm-bytes 120% --timeout 90s
stress-ng --vm 2 --vm-bytes 150% --timeout 90s       # added because nothing was killed at 120%
echo "=== AFTER $(date) ===" >> memory-pressure.txt
free -h >> memory-pressure.txt
```

| | Before | During (worst) | After |
|---|---|---|---|
| Available | 1.2 GiB (18:37:39) | 15 MiB (19:22:18, again at 19:23:26) | 1.3 GiB (19:26:07) |
| Swap used | 0 B (18:37:39) | 1.0 GiB at the worst-available moment (19:22:18); peak 1.4 GiB (19:23:17) | 56 MiB (19:26:07) |

What happened (times from `memory-pressure.txt`):

- **18:37:39 – 18:41:47, idle.** Used about 370 MiB, available 1.2 GiB, swap 0 B.
- **First load, from about 18:41:48.** Available dropped to 580 MiB at 18:41:51; swap reached 335 MiB at 18:41:54; by 18:42:06 available was back to 790 MiB and swap 0 B.
- **Second load, from about 18:49:12.** Used 1.3 GiB / available 324 MiB at 18:49:15, swap 242 MiB at 18:49:18.
- **Third load, from about 18:59:40.** Available fell to 59 MiB at 18:59:47 and to 42 MiB at 19:00:12; swap grew to 330 MiB by 19:00:24.
- **Fourth load (150%), from about 19:22:02 to 19:23:32 — the worst one.** Available dropped to 49 MiB at 19:22:02 and stayed between 15 and 64 MiB for about 90 seconds (close to zero). Swap grew to about 1 GiB by 19:22:12 and kept jumping up and down (1.0 GiB, 490 MiB, 251 MiB, 607 MiB, 988 MiB, ... 1.4 GiB at 19:23:17, 247 MiB at 19:23:20) — pages were constantly being swapped out and back in. At 19:23:32 the load ended: available 1.3 GiB, swap 58 MiB.
- **Responsiveness.** The guest became slow and partly froze during the loads (typing and the terminal lagged). The recorder, which should write a sample every 3 seconds, wrote nothing for about 6.5 minutes after the first load (18:42:06 → 18:48:35), about 8 minutes after the second (18:49:36 → 18:57:45) and about 8 minutes after the third (19:00:28 → 19:08:27); during the fourth load samples kept coming every 3 seconds but with strongly varying numbers.
- **What happened to the process.** No `stress-ng` worker was reported as killed by the kernel in the kernel log (see below); the loads ended at their timeout and the system returned to normal: after the loads available memory was 1.3 GiB (19:23:32 onwards) and swap stayed at 56–58 MiB.

### What the kernel did

Evidence: `evidence/kernel-log-extract.txt`. The file contains the executed command and confirms that no OOM events were found.

Command:

```
journalctl -k --since "30 min ago" | grep -i -E -B3 -A8 "out of memory|oom-kill"
```

Result: the search returned no matching lines - the kernel log contains no "Out of memory: Killed process ..." or "oom-kill:" entries, so the OOM killer did **not** fire during our test, even at 150% of RAM.

Explanation: the machine had 1.6 GiB of RAM and 2.0 GiB of swap. Under load, available memory went down to 15 MiB, but the swap never filled up (the peak was 1.4 GiB out of 2.0 GiB), so the kernel could always free pages by writing them to swap. The OOM killer is only a last resort, when the kernel cannot reclaim enough memory; here the only visible effect was heavy swapping (thrashing), which explains the lags and the freezes of the guest.

### Windows

Command:

```powershell
Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Microsoft-Windows-Resource-Exhaustion-Detector'} -MaxEvents 5 | Format-List TimeCreated, Id, Message
```

Result:

```
PS C:\WINDOWS\system32> Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Microsoft-Windows-Resource-Exhaustion-Detector'} -MaxEvents 5 | Format-List TimeCreated, Id, Message
Get-WinEvent : No events were found that match the specified selection criteria.
At line:1 char:1
+ Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Micro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (:) [Get-WinEvent], Exception
    + FullyQualifiedErrorId : NoMatchingEventsFound,Microsoft.PowerShell.Commands.GetWinEventCommand
```

Explanation: the empty result is valid — the Windows guest was not put under memory pressure (the experiment was done on the Linux guest only) and never ran low on virtual memory. Windows does **not** kill processes like the Linux OOM killer: when the commit charge gets close to the commit limit (RAM + pagefile) it warns the user and allocations start to fail, and the Resource-Exhaustion-Detector then writes **event ID 2004**, which names the processes that used the most memory. That event would appear here if we pushed the commit charge up, for example with Sysinternals Testlimit. (Process Explorer showed a Commit Charge of about 52% during Part 1, well below the limit.)

## What did not work the first time

- `pgrep -a VBoxService` printed nothing on Ubuntu at first: the Guest Additions service was not installed/running, so there was no process to look at. Fixed by installing `virtualbox-guest-utils` and starting the service; after that VBoxService had PID 2089.
- In htop there is no "Columns" menu in our version (htop 3.x): the PPID column is added under F2 → Screens → Available Columns.
- `{ echo "=== BEFORE ..."; free -h; } > memory-pressure.txt` failed with `syntax error near unexpected token '}'`; rewritten as separate `echo ... >` and `free -h >>` lines.
- `stress-ng` first answered `no stress workers invoked` (options were not parsed correctly after copy-pasting); retyping the options by hand fixed it.
- Even at 120% of RAM nothing was killed, so we added a 150% run (the worst one, 19:22–19:23), and the OOM killer still did not fire because swap absorbed the load.

## Evidence files

- [evidence/task-manager.png](evidence/task-manager.png) — Task Manager (Details tab) with the VBoxService.exe row (PID 1888) and the columns PID, private working set, working set, commit size, handles and threads.
- [evidence/process-explorer.png](evidence/process-explorer.png) — Process Explorer with the same process (PID 1888): the process tree with its parent services.exe (PID 876), the memory, handles and threads columns, and the lower pane listing the open handles.
- [evidence/htop.png](evidence/htop.png) — htop tree view on Ubuntu with the VBoxService process (PID 2089, PPID 1) highlighted, showing VIRT, RES, SHR, CPU% and MEM%.
- [evidence/memory-pressure.txt](evidence/memory-pressure.txt) — `free -h` recorded every 3 seconds: the BEFORE block (18:37:39), the four stress-ng loads (worst available memory 15 MiB, swap peak 1.4 GiB) and the AFTER block (19:26:07).
- [evidence/kernel-log-extract.txt](evidence/kernel-log-extract.txt) — the executed `journalctl -k` search command and its result; no OOM events were found during the memory pressure test.
