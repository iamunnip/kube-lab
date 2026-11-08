---
type: Network
title: Private network
description: The 192.168.56.0/24 VirtualBox host-only network shared by all nodes and your computer. All Kubernetes traffic uses it.
tags: [network, virtualbox, host-only]
resource: ../../../vagrant/single-control-plane/Vagrantfile
sources:
  - id: adr-0001
    resource: ../../../docs/adr/0001-use-vagrant-and-virtualbox.md
    title: "ADR 0001: Use Vagrant and VirtualBox"
  - id: adr-0005
    resource: ../../../docs/adr/0005-node-sizes-and-ips.md
    title: "ADR 0005: Node names, sizes and fixed IPs"
---

# Facts

| Field            | Value                                                  |
|------------------|--------------------------------------------------------|
| Range            | `192.168.56.0/24`                                      |
| VirtualBox type  | Host-only                                              |
| Card in each VM  | Card 2, `enp0s8`, VirtIO (`nic_type: "virtio"`)        |
| Your computer    | `192.168.56.1` on `vboxnet0`                           |
| Used addresses   | `.10` [control-plane](../nodes/control-plane.md), `.11` [node-1](../nodes/node-1.md), `.12` [node-2](../nodes/node-2.md) |
| Reserved for HA  | `.20` (kube-vip virtual IP) and `.21` to `.24`, see [ha-control-plane](../layouts/ha-control-plane.md) |
| Free addresses   | `.13` to `.19`, `.25` to `.254`                        |
| Allowed by VirtualBox | `192.168.56.0/21` by default on Linux and macOS[^adr-0001] |
| Set by           | The `private_network` line in each layout's `Vagrantfile` |

# Used for

- Node to node traffic, including all Kubernetes traffic.
- Your computer to node traffic: Ansible on port `22`, kubectl on port `6443`.

# Rules

- Kubernetes must use this network for its node IP, never the [NAT network](nat.md).
- Must not overlap any [cluster address range](cluster-ranges.md).
- Not reachable from other computers on your home network.

# Learn more

- [Lab networking](../../../docs/diataxis/explanation/lab-networking.md)

[^adr-0001]: ADR 0001: Use Vagrant and VirtualBox
