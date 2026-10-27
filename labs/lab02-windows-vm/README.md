Lab 2 — A Windows guest, and a network between them

Pair: Dinmukhamed / Amira

Machine: OMEN by HP Laptop Arch Linux 16GB 16CPU, guests Ubuntu Server + Windows 11 Enterprise eval

Date: <23.10.2026>

What I did

Downloaded Windows 11 Enterprise evaluation. Licence: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise
Created lab02-windows: 2 vCPU, 4 GB, 64 GB thin, UEFI + Secure Boot, vTPM.
Network mode: NAT, both guests. Why: I used NAT Network because both guests must see each other and internet.
Ping Ubuntu → Windows timed out because Windows Defender Firewall blocks incoming echo requests by default. Fixed with: `New-NetFirewallRule -DisplayName "Allow ping in (ICMPv4)" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow`

Overcommitment: set 10 GB + 10 GB on a 16 GB host.

Network diagram
```
              Internet
                 |
      Host laptop 192.168.1.9
                 |
     [VirtualBox NAT · NatNetwork · 10.0.2.0/24]
          /                        \
lab01-ubuntu                  lab02-windows
10.0.2.5                      10.0.2.3
```
Result

	Before	After
Host RAM in use	5GiB	14GiB

Available	9GiB	554MiB

Committed / Swap used	3.2GiB	839MiB

What I noticed	—	Host system freezes when VMs have memory overcommited.

What did not work the first time

At the first time freeze was so strong I couldn't take a screenshot.

Evidence

evidence/ipconfig-windows.txt — Windows address

evidence/ip-addr-linux.txt — Ubuntu address

evidence/ping-both-ways.txt — both directions and the internet

evidence/host-memory-before.png — host memory, guests off

evidence/host-memory-after.png — host memory, both guests overcommitted
