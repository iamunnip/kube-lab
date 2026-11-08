---
type: Cluster Layout
title: single-control-plane
description: 1 control plane and 2 nodes, defined in vagrant/single-control-plane/Vagrantfile. The layout every current tutorial uses.
tags: [layout, vagrant, cluster]
resource: ../../../vagrant/single-control-plane/Vagrantfile
status: draft
sources:
  - id: adr-0007
    resource: ../../../docs/adr/0007-one-folder-per-cluster-layout.md
    title: "ADR 0007: One folder and Vagrantfile per cluster layout"
---

# Facts

| Field                   | Value                                                       |
|-------------------------|-------------------------------------------------------------|
| Folder                  | `vagrant/single-control-plane/`[^adr-0007]                  |
| Status                  | Ready. Tested on 2026-11-08                                 |
| VMs                     | [control-plane](../nodes/control-plane.md), [node-1](../nodes/node-1.md), [node-2](../nodes/node-2.md) |
| Control planes          | 1                                                           |
| Nodes                   | 2                                                           |
| API address             | `192.168.56.10:6443`                                        |
| IP range                | `192.168.56.10` to `192.168.56.12`                          |
| VirtualBox name prefix  | `kube-lab-single-`[^adr-0007]                               |
| Size per VM             | 3 CPU, 6 GB, 10 GB [OS disk](../storage/os-disk.md), 20 GB [data disk](../storage/data-disk.md) |
| Totals                  | 9 CPUs, 18 GB memory                                        |
| Vagrant state           | `vagrant/single-control-plane/.vagrant/` (not in git)       |
| Survives a control plane failure | No                                                 |

# Learn more

- [Cluster layouts](../../../docs/diataxis/explanation/cluster-layouts.md)
- [Create the VMs](../../../docs/diataxis/tutorials/02-create-vms.md)

[^adr-0007]: ADR 0007: One folder and Vagrantfile per cluster layout
