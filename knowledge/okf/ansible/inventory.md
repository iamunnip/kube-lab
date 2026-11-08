---
type: Ansible Inventory
title: single-control-plane inventory
description: hosts.ini with the groups control_plane, nodes and cluster. Each host has ansible_host and vagrant_machine, which picks its Vagrant SSH key, also under delegate_to.
tags: [ansible, inventory, ssh]
resource: ../../../ansible/inventories/single-control-plane
status: stable
sources:
  - id: adr-0013
    resource: ../../../docs/adr/0013-short-lived-join-tokens.md
    title: "ADR 0013: Short lived join tokens, made per node by Ansible"
---

# Facts

| Field                  | Value                                                          |
|------------------------|----------------------------------------------------------------|
| File                   | `ansible/inventories/single-control-plane/hosts.ini`           |
| Groups                 | `control_plane` (control-plane), `nodes` (node-1, node-2), `cluster` (all) |
| Host settings          | `ansible_host` (private IP), `vagrant_machine` (VM name in the Vagrantfile) |
| SSH key                | `vagrant/single-control-plane/.vagrant/machines/<vagrant_machine>/virtualbox/private_key`, set in `lab.yml` |
| Why `vagrant_machine`  | With `delegate_to`, `inventory_hostname` keeps the original host's name, so the key of the wrong VM was used[^adr-0013] |
| Settings               | `group_vars/all/`: `cluster.yml`, `kubeadm.yml`, `rke2.yml`, `cilium.yml`, `os.yml`, `lab.yml` |
| Default in             | `ansible/ansible.cfg` (`inventory =`)                          |

# Related

- The VMs: [control-plane](../nodes/control-plane.md), [node-1](../nodes/node-1.md), [node-2](../nodes/node-2.md).
- Used by every role, for example [kubeadm](kubeadm.md).

# Learn more

- [Ansible settings](../../../docs/diataxis/reference/ansible-settings.md)

[^adr-0013]: ADR 0013: Short lived join tokens, made per node by Ansible
