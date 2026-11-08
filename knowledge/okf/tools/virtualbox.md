---
type: Tool
title: VirtualBox
description: The hypervisor that runs the lab VMs on your computer.
tags: [tool, hypervisor, host]
resource: https://www.virtualbox.org/
sources:
  - id: adr-0001
    resource: ../../../docs/adr/0001-use-vagrant-and-virtualbox.md
    title: "ADR 0001: Use Vagrant and VirtualBox"
---

# Facts

| Field                  | Value                                               |
|------------------------|-----------------------------------------------------|
| Runs on                | Your computer                                       |
| Job                    | Runs [control-plane](../nodes/control-plane.md), [node-1](../nodes/node-1.md), [node-2](../nodes/node-2.md) |
| Controlled by          | [Vagrant](vagrant.md). You do not need its GUI      |
| Kernel module          | `vboxdrv`                                           |
| Version on main lab computer | 7.2.22                                        |
| Disks it holds         | [OS disk](../storage/os-disk.md) and [data disk](../storage/data-disk.md) per VM, in the VirtualBox machine folder |
| Networks it provides   | [NAT network](../networks/nat.md), [private network](../networks/private-network.md) |
| Chosen in              | ADR 0001[^adr-0001]                                 |

# Known issues

- Can fail with `VERR_VMX_IN_VMX_ROOT_MODE` when the Linux KVM module holds the CPU virtualization feature. Fix: [Fix the VirtualBox and KVM error](../../../docs/diataxis/how-to/fix-virtualbox-kvm-error.md).
- With Secure Boot on, its kernel modules must be signed.

# Learn more

- [Install the tools](../../../docs/diataxis/tutorials/01-install-tools.md)

[^adr-0001]: ADR 0001: Use Vagrant and VirtualBox
