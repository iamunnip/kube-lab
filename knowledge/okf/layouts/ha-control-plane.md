---
type: Cluster Layout
title: ha-control-plane
description: Planned. 3 control planes and 1 node, with a kube-vip virtual IP in front of the API servers. Will live in vagrant/ha-control-plane/.
tags: [layout, vagrant, cluster, ha, kube-vip]
status: draft
sources:
  - id: adr-0007
    resource: ../../../docs/adr/0007-one-folder-per-cluster-layout.md
    title: "ADR 0007: One folder and Vagrantfile per cluster layout"
---

# Facts

| Field                   | Value                                                       |
|-------------------------|-------------------------------------------------------------|
| Folder                  | `vagrant/ha-control-plane/` (not created yet)[^adr-0007]    |
| Status                  | Planned                                                     |
| VMs                     | `control-plane-1`, `control-plane-2`, `control-plane-3`, `node-1` |
| Control planes          | 3                                                           |
| Nodes                   | 1                                                           |
| Virtual IP (kube-vip)   | `192.168.56.20`, API on port `6443`                         |
| VM IPs                  | `192.168.56.21` to `192.168.56.24`[^adr-0007]               |
| VirtualBox name prefix  | `kube-lab-ha-`[^adr-0007]                                   |
| Size per VM             | Same as [single-control-plane](single-control-plane.md): 3 CPU, 6 GB, 10 GB OS disk and 20 GB data disk |
| Totals                  | 12 CPUs, 24 GB memory                                       |
| Survives a control plane failure | Yes, 1 of 3 (etcd keeps quorum with 2)             |

# How kube-vip works here

- Runs on all 3 control planes and elects one leader.
- The leader answers for the virtual IP on the [private network](../networks/private-network.md).
- If the leader fails, another control plane takes the virtual IP.
- kubectl and `node-1` always use the virtual IP, never a single control plane's IP.

# Learn more

- [Cluster layouts](../../../docs/diataxis/explanation/cluster-layouts.md)

[^adr-0007]: ADR 0007: One folder and Vagrantfile per cluster layout
