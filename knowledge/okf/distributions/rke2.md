---
type: Kubernetes Distribution
title: RKE2
description: Security focused Kubernetes from SUSE Rancher, with its own containerd and etcd. The lab sets cni none and adds Cilium. Chosen with k8s_distro rke2 in cluster.yml.
tags: [kubernetes, distribution, rke2]
resource: https://docs.rke2.io/
status: stable
sources:
  - id: adr-0010
    resource: ../../../docs/adr/0010-one-site-playbook-driven-by-vars.md
    title: "ADR 0010: One site playbook, every setting in vars files"
---

# Facts

| Field                  | Value                                                          |
|------------------------|----------------------------------------------------------------|
| Made by                | SUSE Rancher                                                   |
| Chosen with            | `k8s_distro: rke2` in `cluster.yml`[^adr-0010]                 |
| Version setting        | `rke2_version`, for example `v1.37.2+rke2r1`                   |
| Installed by           | The [rke2](../ansible/rke2.md) role, from the RKE2 release tarball (no install script) |
| Server                 | `rke2-server` on [control-plane](../nodes/control-plane.md)    |
| Agents                 | `rke2-agent` on [node-1](../nodes/node-1.md) and [node-2](../nodes/node-2.md), join on port 9345 with a token |
| Data dir               | `/data/rancher/rke2` (images, containers, etcd)                |
| kubelet data           | `/data/kubelet`                                                |
| Pod network            | `cni: none`, then [Cilium](../ansible/cilium.md) with Helm      |
| Ingress controller     | `rke2_ingress_controller`, lab: `none` (Traefik off)           |
| Service load balancer  | `rke2_enable_servicelb`, lab: `false`                          |
| Pod and service ranges | `10.244.0.0/16`, `10.96.0.0/12`, the same as kubeadm           |
| Config file on the VM  | `/etc/rancher/rke2/config.yaml`, from the rke2 role's `config-server.yaml.j2` or `config-agent.yaml.j2` |
| All settings           | `group_vars/all/rke2.yml`                                      |
| Upgrade                | Raise `rke2_version`, run `site.yml`: new tarball unpacked, server first, then agents |
| Remove                 | `reset.yml` (runs `rke2-uninstall.sh` with `RKE2_DATA_DIR`)    |
| Tested                 | 2026-11-08: v1.37.1 install, upgrade to v1.37.2, reset (install script). Again on fresh VMs with the tarball install and the role templates: 3 nodes Ready on v1.37.2+rke2r1, no kube-proxy, `verify-network.yml` `failed=0`, rerun `changed=0` |

# Learn more

- [kubeadm vs RKE2](../../../docs/diataxis/explanation/kubeadm-vs-rke2.md)
- [Installing Kubernetes explained](../../../docs/diataxis/explanation/installing-kubernetes-explained.md)

[^adr-0010]: ADR 0010: One site playbook, every setting in vars files
