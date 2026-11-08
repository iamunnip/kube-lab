---
type: Tool
title: Vagrant
description: Creates, starts, stops and deletes the lab VMs from the Vagrantfile. Does not install any software inside them.
tags: [tool, vms, host]
resource: https://developer.hashicorp.com/vagrant
sources:
  - id: adr-0001
    resource: ../../../docs/adr/0001-use-vagrant-and-virtualbox.md
    title: "ADR 0001: Use Vagrant and VirtualBox"
  - id: adr-0004
    resource: ../../../docs/adr/0004-keep-vms-and-provisioning-separate.md
    title: "ADR 0004: Vagrant only makes VMs, Ansible installs everything"
---

# Facts

| Field                  | Value                                               |
|------------------------|-----------------------------------------------------|
| Runs on                | Your computer, in a layout folder such as `vagrant/single-control-plane/` |
| Reads                  | The `Vagrantfile` in the current folder             |
| Provider               | [VirtualBox](virtualbox.md)                         |
| Box                    | `cloud-image/ubuntu-26.04`                          |
| Disks                  | Set with `vm.disk`, built in, no plugin or flag needed in 2.4.9 |
| State folder           | `.vagrant/` inside the layout folder (VM IDs and SSH keys, not in git) |
| Box cache              | `~/.vagrant.d/boxes/`                               |
| Version on main lab computer | 2.4.9                                         |
| Provisioners           | None, by design[^adr-0004]                          |

# Job in the lab

- Creates the 3 [nodes](../nodes/index.md) with fixed names, IPs, 3 CPUs and 6 GB memory each.
- Adds the [private network](../networks/private-network.md) card.
- Grows the [OS disk](../storage/os-disk.md) to 20 GB and adds the [data disk](../storage/data-disk.md).
- Makes one SSH key per VM, which [Ansible](ansible.md) reuses.

# Learn more

- [The Vagrantfile explained](../../../docs/diataxis/explanation/vagrantfile-explained.md)
- [Vagrant commands](../../../docs/diataxis/reference/vagrant-commands.md)

[^adr-0004]: ADR 0004: Vagrant only makes VMs, Ansible installs everything
