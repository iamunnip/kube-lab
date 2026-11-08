---
type: Tool
title: Ansible
description: Runs on your computer and installs and configures Kubernetes on the lab VMs over SSH.
tags: [tool, provisioning, host]
resource: https://docs.ansible.com/
status: stable
sources:
  - id: adr-0004
    resource: ../../../docs/adr/0004-keep-vms-and-provisioning-separate.md
    title: "ADR 0004: Vagrant only makes VMs, Ansible installs everything"
---

# Facts

| Field                  | Value                                               |
|------------------------|-----------------------------------------------------|
| Runs on                | Your computer                                       |
| Connects to            | Each node's [private network](../networks/private-network.md) IP on port `22` |
| Login                  | User `vagrant` with the key from `vagrant/<layout>/.vagrant/machines/<vagrant_machine>/virtualbox/private_key`, see [inventory](../ansible/inventory.md) |
| Needs on the VMs       | SSH and Python 3. Ubuntu already has both           |
| Lab files              | `ansible/`: `ansible.cfg`, `requirements.yml`, `inventories/`, `roles/` |
| Playbooks              | `site.yml` (install or upgrade), `prepare.yml`, `reset.yml`, `verify-network.yml` |
| Roles                  | [prereq](../ansible/prereq.md), [k8s_prepare](../ansible/k8s-prepare.md), [kubeadm](../ansible/kubeadm.md), [rke2](../ansible/rke2.md), [cilium](../ansible/cilium.md) |
| Collections used       | `ansible.builtin`, `ansible.posix` 2.2.2, `community.general` 13.5.0, `kubernetes.core` 6.6.0 (bundled with the `ansible` package, listed in `ansible/requirements.yml`) |
| Version on main lab computer | ansible-core 2.22.0                           |

# Job in the lab

- Prepares every node for Kubernetes.
- Installs one distribution at a time, picked by `k8s_distro` in `cluster.yml`: [kubeadm](../distributions/kubeadm.md) or [RKE2](../distributions/rke2.md).
- Sets each node's Kubernetes node IP to its private network address.
- Resets a node so another distribution can be installed.
- Does all installing, so Vagrant does not.[^adr-0004]

# Learn more

- [How the lab works](../../../docs/diataxis/explanation/how-the-lab-works.md)

[^adr-0004]: ADR 0004: Vagrant only makes VMs, Ansible installs everything
