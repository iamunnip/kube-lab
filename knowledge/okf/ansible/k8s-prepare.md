---
type: Ansible Role
title: k8s_prepare
description: Kubernetes level basics for every VM. Makes the Kubernetes data folders on /data, turns swap off, loads kernel modules, sets sysctls and adds all nodes to /etc/hosts.
tags: [ansible, role, kubernetes, kernel]
resource: ../../../ansible/roles/k8s_prepare
status: stable
sources:
  - id: adr-0008
    resource: ../../../docs/adr/0008-prereq-and-k8s-prepare-roles.md
    title: "ADR 0008: Two Ansible roles before Kubernetes"
---

# Facts

| Field                | Value                                                         |
|----------------------|---------------------------------------------------------------|
| Folder               | `ansible/roles/k8s_prepare/`                                  |
| Run by               | `ansible/prepare.yml`, after [prereq](prereq.md)[^adr-0008]   |
| Needs                | `/data` mounted. Stops with a message if it is not            |
| Tested               | 2026-11-08 on all 3 VMs, survives a reboot                    |
| Tags                 | `data_dirs`, `swap`, `kernel`, `hosts`                        |

# Steps

| Tag          | What it does                                                                  |
|--------------|-------------------------------------------------------------------------------|
| `data_dirs`  | Makes `/data/containerd`, `/data/rancher`, `/data/kubelet`, `/data/storage`   |
| `swap`       | `swapoff --all` if swap exists, comments out swap lines in `/etc/fstab`       |
| `kernel`     | Loads `overlay` and `br_netfilter`, writes `/etc/modules-load.d/kubernetes.conf`, sets 3 sysctls in `/etc/sysctl.d/99-kubernetes.conf` |
| `hosts`      | Adds a `kube-lab nodes` block with every node's private IP to `/etc/hosts`    |

# Which distribution uses which folder

| Folder             | Distribution     | Setting the distribution role will use |
|--------------------|------------------|----------------------------------------|
| `/data/containerd` | [kubeadm](../distributions/kubeadm.md) | containerd `root`   |
| `/data/rancher`    | [RKE2](../distributions/rke2.md) | `data-dir` (`/data/rancher/rke2`) |
| `/data/kubelet`    | all              | kubelet `root-dir`                     |
| `/data/storage`    | all              | app volumes later                      |

# Learn more

- [The Ansible roles explained](../../../docs/diataxis/explanation/ansible-roles-explained.md)

[^adr-0008]: ADR 0008: Two Ansible roles before Kubernetes
