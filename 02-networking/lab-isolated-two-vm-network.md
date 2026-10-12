# Lab: building an isolated two-VM network and troubleshooting it

**Date:** 2026-10-12
**Stage:** Networking fundamentals (and virtualization)
**Tools:** VirtualBox, Kali Linux, Android-x86, ADB, Linux CLI (`ip`, `ping`, `iptables`)

## Goal

Build a small isolated lab with two virtual machines on their own private network, get them talking to each other, and practise diagnosing the connection when it didn't work.

## What I set up

- **Attacker/analysis VM:** Kali Linux (official VirtualBox image, SHA-256 checksum verified before use).
- **Target VM:** Android-x86 9.0 (ISO checksum verified, then a clean manual install: partitioned the virtual disk with cfdisk, set the boot flag, formatted ext4, installed GRUB).
- **Network:** a VirtualBox NAT Network, `10.0.9.0/24`, with both VMs attached so they shared one private subnet and could not reach anything else.
- Enabled ADB over TCP on the Android VM and gave it the static address `10.0.9.200`.

## The problem

From Kali (`10.0.9.3`) I could not reach the Android VM (`10.0.9.200`). Every connection attempt hung.

## How I diagnosed it (layer by layer)

1. **Confirmed my own IP.** `ip -4 a` on Kali showed `10.0.9.3/24` on eth0. So Kali was on the right network.
2. **Tested reachability.** `ping -c 3 10.0.9.200` gave 100% packet loss.
3. **Checked Layer 2.** `ip neigh show 10.0.9.200` returned the target's real MAC address (`08:00:27:42:ac:b6`). So ARP resolved: the two machines *could* see each other at the hardware level. The problem was higher up.
4. **Checked the firewall.** Flushed the target's `iptables` rules and set the policies to ACCEPT. Re-tested: still 100% loss.
5. **Found the root cause.** The Android VM had only booted to a text console, not its full graphical system, so its network service (`netd`) never started. `netd` is what sets up Android's routing policy, so the IP address existed on the interface but traffic was never actually routed. ARP worked (that is the network card), but nothing above it did.

## What I learned

- A methodical, layer-by-layer check (my IP → ping → ARP → firewall → the service itself) narrows a network fault quickly, instead of guessing.
- ARP resolving but ping failing is a useful signal: the link works, the problem is at IP/routing/firewall level or above.
- An IP address on an interface does not by itself mean networking works. The OS services behind it have to be running.
- Verifying a download's checksum before using it is a basic, worthwhile habit.

## Honest note

The root cause traced back to the host hardware being unable to render the Android VM's graphical environment, which is why its network service never came up. The transferable skills here are the virtualization setup and the structured network troubleshooting, not the specific target.
