# Lab 1 — Your first virtual machine

Pair: Dinmukhamed and Amira                
Machine: OMEN by HP Laptop Arch Linux
Date: 17.10.2026

## What I did

Steps, with commands in code blocks. Not prose about the steps.

Installed VirtualBox 7.2.20. Licence: https://www.virtualbox.org/wiki/GPLv3
Created VM lab01-ubuntu: 4 vCPU, 2 GB RAM, 25 GB dynamic disk, NAT.
Installed Ubuntu Server, took snapshot clean-install.
Broke it: sudo rm -rf /usr/bin
Rolled back to clean-install

## Result

The VM boots; the break-and-rollback cycle is shown in the evidence below.

## What did not work the first time

Nothing

## Evidence

[Evidence](vm-settings.png) — CPU, memory and disk as configured
[Evidence](guest-release.txt) — Ubuntu Server is running in the guest
[Evidence](broken.png) — the guest in its broken state
[Evidence](snapshot-list.png) — the snapshot, after the rollback
