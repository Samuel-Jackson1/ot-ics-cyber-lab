# Learning log

Newest first. One entry per study week: what I studied, what I built, what broke, what I'd do differently.

## 2026-10-12

- Installed **Kali Linux** in VirtualBox (verified the image checksum first).
- Did a clean **Android-x86** install from scratch: partitioned the virtual disk with cfdisk, set the boot flag, formatted ext4, installed GRUB.
- Built an **isolated two-VM lab network** (VirtualBox NAT Network, 10.0.9.0/24) and practised **network troubleshooting**: `ip addr`, `ping`, `ip neigh` (ARP), `iptables`.
- Traced a VM-to-VM connectivity failure layer by layer down to the target's network service not running. Full write-up: [02-networking/lab-isolated-two-vm-network.md](02-networking/lab-isolated-two-vm-network.md).
- Lesson: an IP on an interface doesn't mean networking works; check the service behind it. ARP resolving but ping failing points above Layer 2.
- Next: Phase 1 proper, building an Ubuntu/Windows VM pair and the Packet Tracer routed-network exercise.

## 2026-09-26

- Set up my learning plan for OT/ICS security: nine stages from computer basics to a defended simulated plant.
- Created this repository and my portfolio site.
- Next: stage 01, building an Ubuntu VM and a Windows VM in VirtualBox.
