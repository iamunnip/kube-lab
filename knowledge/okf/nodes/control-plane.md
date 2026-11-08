---
type: Virtual Machine
title: control-plane
description: Manages the Kubernetes cluster. IP 192.168.56.10, 3 CPU, 6 GB memory, 10 GB OS disk, 20 GB data disk, Ubuntu 26.04.
tags: [vm, control-plane]
resource: ../../../vagrant/single-control-plane/Vagrantfile
status: stable
sources:
  - id: adr-0005
    resource: ../../../docs/adr/0005-node-sizes-and-ips.md
    title: "ADR 0005: Node names, sizes and fixed IPs"
  - id: adr-0006
    resource: ../../../docs/adr/0006-two-disks-per-node.md
    title: "ADR 0006: Two disks per node"
---

# Facts

| Field              | Value                                                        |
|--------------------|--------------------------------------------------------------|
| Name and hostname  | `control-plane`                                                     |
| VirtualBox name    | `kube-lab-single-control-plane`                                            |
| CPUs               | 3[^adr-0005] (needs the IO-APIC on, set in the `Vagrantfile`) |
| Memory             | 6144 MB[^adr-0005]                                           |
| OS                 | Ubuntu 26.04 LTS, box `cloud-image/ubuntu-26.04`             |
| Network card 1     | `10.0.2.15` on the [NAT network](../networks/nat.md)         |
| Network card 2     | `192.168.56.10` on the [private network](../networks/private-network.md), VirtIO card |
| Disk 1             | [OS disk](../storage/os-disk.md), 10 GB, from the box, not resized[^adr-0006] |
| Disk 2             | [Data disk](../storage/data-disk.md) `control-plane-data`, 20 GB, file `control-plane-data.vdi`[^adr-0006] |
| VM folder          | `<VirtualBox machine folder>/kube-lab-single-control-plane/`                          |
| Login              | `vagrant ssh control-plane`, user `vagrant`                         |
| SSH key            | `vagrant/single-control-plane/.vagrant/machines/control-plane/virtualbox/private_key`            |

# Role in the cluster

- Runs the Kubernetes control plane: API server on port `6443`, etcd, scheduler and controller manager.
- Called the **server** in [RKE2](../distributions/rke2.md).
- Runs `kubeadm init` in [kubeadm](../distributions/kubeadm.md).
- Installed first. [node-1](node-1.md) and [node-2](node-2.md) join it.
- Its 6 GB memory exceeds RKE2's documented 4 GB server minimum.

# Defined in

- The `NODES` list and the `vm.disk` lines in the `Vagrantfile`.

# Learn more

- [Nodes and IPs](../../../docs/diataxis/reference/nodes-and-ips.md)
- [Kubernetes basics](../../../docs/diataxis/explanation/kubernetes-basics.md)

[^adr-0005]: ADR 0005: Node names, sizes and fixed IPs
[^adr-0006]: ADR 0006: Two disks per node
