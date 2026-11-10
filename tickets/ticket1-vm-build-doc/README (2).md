# Ticket 1 — New developer starts Monday, needs a test machine

## 1. What was asked

> **From:** Aigerim T. (Office Manager)
> **Subject:** test machine for new dev
>
> Hi, we have someone starting on Monday and he needs a test computer set up. Nothing special, just so he can try things without breaking anything. Can you write down how to do it so I can do the next one myself? Thanks

## 2. What was actually needed


**What the request left out, what I would ask, what I assumed instead**

| Left out | What I would ask Aigerim | What I assumed instead |
|---|---|---|
| Which operating systems | What will the developer build and test: Linux, Windows, both? | Both: Ubuntu Server 24.04 LTS and Windows 11 Enterprise evaluation, because a developer tests on both |
| Physical or virtual | Can the test machine be a virtual machine on an existing computer? | Virtual: it can be copied, restored and thrown away, a physical PC cannot |
| Size of the host | Which computer will host it, how much RAM and disk? | My laptop, numbers from Lab 0 (see section 3) |
| Network | Does it need the internet? Must the two machines see each other? Is the office network filtered? | Internet yes; both machines see each other; they must not sit on the real network → NAT Network |
| Hypervisor, licence, ISO | Is anything allowed to be bought? Is a 90-day Windows trial acceptable? | VirtualBox 7 (free, GPLv2), no Extension Pack, evaluation ISOs (free, no key) |
| Reader | Who follows the document? | Aigerim: one action per step, a screenshot at every step, a check after steps |

**Where the request is wrong**
- *"Nothing special"* hides the decisions that matter: which OS, how much of the host, which network. Aigerim cannot judge these, so I had to.
- *"Without breaking anything"* means two different things. The **host** is protected because a VM lives in its own virtual disk and memory, and I cap its RAM and CPU so it cannot starve the host. The **VM itself** can still be broken, so the document needs a clean snapshot and a **tested** way back to it.
- *"A test computer"* suggests hardware, but a virtual machine is the cheaper and safer way to meet the same need.

## 3. What I did

**Host and resource calculation** 

| | Total | Host at idle | Ubuntu guest | Windows guest | Headroom |
|---|---|---|---|---|---|
| RAM | 16 GB | about 8.6 GB | 2 GB | 4 GB | 16 − 8.6 − 6 = **1.4 GB** |
| Disk (maximum) | 810 GB free | — | 25 GB | 64 GB | 810 − 89 = **721 GB** |

Disks are dynamic (they grow as used), so the free-disk check is against the *maximum* sizes. [Add one sentence: what happened to host memory with both guests running.]

**Steps** (full instructions with screenshots and reasons are in [BUILD.md](BUILD.md))
1. Checked that virtualization is enabled on the host.
2. Downloaded VirtualBox, the Ubuntu Server ISO and the Windows 11 Enterprise evaluation ISO.
3. Created the NAT Network `osarch-net`.
4. Built `test-dev-vm` (Ubuntu Server) and `win11-eval` (Windows 11), both on `osarch-net`.
5. Verified inside each guest:
   ```bash
   uname -a
   lsb_release -a
   ```
6. Took a snapshot `clean-install` of each guest and proved the rollback works.

**Settings that differ from the default**

| Setting | Default | Mine | Why |
|---|---|---|---|
| Skip Unattended Installation | [state of the box in your wizard] | [yours] | [your reason, e.g. I choose the user and the disk layout myself and see every step] |
| Network adapter | NAT | NAT Network `osarch-net` | Plain NAT isolates the two guests from each other; NAT Network lets them talk and still reach the internet without touching the real network |
| Ubuntu RAM / CPU | [wizard value] | 2048 MB / 2 | Enough for a server install, leaves the host responsive |
| Ubuntu disk | [wizard value] | 25 GB, dynamic | Room for updates and logs without taking 25 GB up front |
| Windows RAM / CPU / disk | [wizard value] | 4096 MB / 2 / 64 GB dynamic | Windows 11 minimums |
| Windows firmware | [wizard value] | EFI, TPM 2.0, Secure Boot | Windows 11 refuses to install without them |
| Windows Firewall | blocks ping | allow ICMPv4 echo (lab rule) | Needed to test guest → guest |
| VirtualBox Extension Pack | not installed | not installed | Free only for personal/educational use; an office would need a paid licence |
| Snapshots | none | `clean-base` on both guests | Lets Aigerim undo any mistake |

**Why NAT Network and not the alternatives**
- *NAT*: internet works, but the guests cannot see each other: fails the requirement.
- *Bridged*: guests appear on the real network with their own MAC address; networks that filter by MAC (the Innotech teaching network, per the brief) will not accept them, and the VMs are exposed.
- *Host-only*: guests see each other but have no internet.
- *NAT Network*: both. **Chosen.**

**Why VirtualBox:** free, same on every host OS, supports TPM 2.0 for Windows 11 from version 7.0. VMware Workstation Pro, Hyper-V (not in Windows Home) and KVM/QEMU (Linux host only) were considered; details are in BUILD.md.

**Both guests reach the internet and see each other.** `evidence/05-ping-between-guests.txt` holds, unedited: Ubuntu → Windows, Windows → Ubuntu, Ubuntu → internet, Windows → internet, all with 0% packet loss.

**One thing that went wrong and how I fixed it.** [Write the real one. Example from my build: I could not copy output out of the VM window, because the mouse stayed captured by the VM. I released it with the host key (Right Ctrl), and saved command output with `| tee file.txt` instead of copying it by hand.]

## 4. Evidence

- [evidence/01-new-vm-wizard.png](evidence/01-new-vm-wizard.png) — the new-VM wizard with my choices for the Ubuntu guest.
- [evidence/01-hardware-settings.png](evidence/01-hardware-settings.png) — Screenshot showing the 2048 MB RAM and 2 CPU cores configuration
- [evidence/02-disk-settings.png](evidence/02-disk-settings.png) — 25 GB disk, dynamically allocated.
- [evidence/03-network-adapter.png](evidence/03-network-adapter.png) — the guest's adapter set to NAT Network `osarch-net`.
- [evidence/04-guest-uname.txt](evidence/04-guest-uname.txt) — `uname -a` and `lsb_release -a` run inside the Ubuntu guest, command lines visible.
- [evidence/05-ping-between-guests.txt](evidence/05-ping-between-guests.txt) — Ubuntu ↔ Windows and each guest → internet, unedited.
- [evidence/06-snapshot-list.png](evidence/06-snapshot-list.png) — the `clean-install` snapshot.

The step-by-step screenshots used in BUILD.md are in [screenshots/](screenshots/).

## 5. What I would do differently

At first, I had trouble getting the text for files 04 and 05 because my mouse cursor wouldn't go inside the virtual machine terminal, and I didn't know how to copy the text easily. Next time, I will remember to use the Ctrl + Alt + Delete to release the mouse or save command outputs directly to files via terminal redirection
