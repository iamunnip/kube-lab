---
type: Network
title: NAT network
description: The 10.0.2.0/24 VirtualBox NAT network inside each VM. Used for internet access and vagrant ssh, never for Kubernetes.
tags: [network, virtualbox, nat]
---

# Facts

| Field            | Value                                                    |
|------------------|----------------------------------------------------------|
| Range            | `10.0.2.0/24`                                            |
| VM address       | `10.0.2.15` on **every** VM                              |
| Card in each VM  | Card 1, `enp0s3`, Intel PRO/1000 (from the box)          |
| Default route    | Yes. Internet traffic leaves through this card           |
| Set by           | VirtualBox and Vagrant, automatically                    |

# Used for

- Downloading packages and container images.
- `vagrant ssh`, through a forwarded port on `127.0.0.1` (often `2222`, `2200`, `2201`).

# Rules

- Each VM has its own separate NAT. VMs cannot reach each other over it.
- Kubernetes must not use it. Because every VM has the same address, nodes cannot be told apart.
- Many tools pick the card with the default route by default, which is this one. The Ansible roles must set the node IP to the [private network](private-network.md) address.

# Learn more

- [Lab networking](../../../docs/diataxis/explanation/lab-networking.md)
