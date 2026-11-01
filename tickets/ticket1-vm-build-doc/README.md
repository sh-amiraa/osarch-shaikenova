# Ticket 1 — VM build document

## 1. What was asked

> "Hi, we have someone starting on Monday and he needs a test computer set up. Nothing special, just so he can try things without breaking anything. Can you write down how to do it so I can do the next one myself? Thanks"

## 2. What was actually needed

The original request was written by a non-technical person, so it left out a lot of important details like how much RAM to use, CPU cores, disk size, and network settings. If I just installed a VM using default settings without thinking, it might crash the host computer or the internet wouldn't work properly for testing. I decided to use an Ubuntu VM in VirtualBox with safe settings (2 GB RAM, 2 CPU cores, and a 25 GB hard drive) to keep the host computer safe, and set up NAT so the VM can easily get internet access. I assumed the user already has VirtualBox installed and a downloaded Ubuntu Server ISO

## 3. What I did

1. Downloaded and set up Oracle VM VirtualBox on my computer
2. Created a new virtual machine for Ubuntu (64-bit)
3. Assigned the hardware resources: 2048 MB of RAM and 2 CPU cores
4. Added a 25 GB virtual hard disk that grows dynamically as needed
5. Set up Network Adapter 1 to use NAT so the VM can connect to the internet
6. Started the VM, installed the OS, and ran commands (`uname -a` and `ping`) to check that everything works
7. Made a clean snapshot called `clean-base` so we can easily restore it if something breaks later

## 4. Evidence

* [evidence/01-new-vm-wizard.png](evidence/01-new-vm-wizard.png) — Screenshot showing the VM creation wizard
* [evidence/02-disk-settings.png](evidence/02-disk-settings.png) — Screenshot of the 25 GB virtual hard drive setup
* [evidence/03-network-adapter.png](evidence/03-network-adapter.png) — Screenshot showing the network adapter set to NAT
* [evidence/04-guest-uname.txt](evidence/04-guest-uname.txt) — Text file with the output of the `uname -a` command from the VM
* [evidence/05-ping-result.txt](evidence/05-ping-result.txt) — Text file showing that internet ping tests were successful
* [evidence/06-snapshot-list.png](evidence/06-snapshot-list.png) — Screenshot of the VirtualBox snapshot manager with our `clean-base` snapshot

## 5. What I would do differently

At first, I had trouble getting the text for files 04 and 05 because my mouse cursor wouldn't go inside the virtual machine terminal, and I didn't know how to copy the text easily. Next time, I would install the VirtualBox Guest Additions right away so the mouse and copying work smoothly from the start
