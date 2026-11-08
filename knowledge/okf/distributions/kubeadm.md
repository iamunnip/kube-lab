---
type: Kubernetes Distribution
title: kubeadm
description: The official Kubernetes tool to build a cluster. The lab adds Ubuntu's containerd and Cilium. Chosen with k8s_distro kubeadm in cluster.yml.
tags: [kubernetes, distribution, kubeadm]
resource: https://kubernetes.io/docs/reference/setup-tools/kubeadm/
status: stable
sources:
  - id: adr-0009
    resource: ../../../docs/adr/0009-kubeadm-with-containerd-and-cilium.md
    title: "ADR 0009: kubeadm with containerd, Cilium with Helm"
  - id: adr-0010
    resource: ../../../docs/adr/0010-one-site-playbook-driven-by-vars.md
    title: "ADR 0010: One site playbook, every setting in vars files"
---

# Facts

| Field                  | Value                                                          |
|------------------------|----------------------------------------------------------------|
| Made by                | The Kubernetes project                                         |
| Chosen with            | `k8s_distro: kubeadm` in `cluster.yml`[^adr-0010]              |
| Version setting        | `kubernetes_version`, for example `1.37.2`                     |
| Installed by           | The [kubeadm](../ansible/kubeadm.md) role, from `site.yml`     |
| Packages               | `kubeadm`, `kubelet`, `kubectl` from `pkgs.k8s.io`, held       |
| Container runtime      | Ubuntu's containerd, root `/data/containerd`[^adr-0009]        |
| Control plane          | `kubeadm init` on [control-plane](../nodes/control-plane.md)   |
| Other nodes            | `kubeadm join --config` on [node-1](../nodes/node-1.md) and [node-2](../nodes/node-2.md), port 6443, with a 10 minute token that is deleted after the join |
| kube-proxy             | Not installed (`proxy.disabled: true`). Cilium does its job, see [service routing](../networks/service-routing.md) |
| etcd data              | `/data/etcd`                                                   |
| kubelet data           | `/data/kubelet`                                                |
| Pod network            | [Cilium](../ansible/cilium.md) with Helm                        |
| Pod and service ranges | `10.244.0.0/16`, `10.96.0.0/12`, see [cluster ranges](../networks/cluster-ranges.md) |
| Config files           | `roles/kubeadm/templates/kubeadm-init.yaml.j2`, `kubeadm-join.yaml.j2` (the real kubeadm YAML) |
| Values                 | `group_vars/all/kubeadm.yml`                                   |
| Upgrade                | Raise `kubernetes_version`, run `site.yml`: `kubeadm upgrade apply`, then `kubeadm upgrade node`, then new kubelet. One minor version at a time |
| Remove                 | `reset.yml`                                                    |

# Learn more

- [kubeadm vs RKE2](../../../docs/diataxis/explanation/kubeadm-vs-rke2.md)
- [Installing Kubernetes explained](../../../docs/diataxis/explanation/installing-kubernetes-explained.md)

[^adr-0009]: ADR 0009: kubeadm with containerd, Cilium with Helm
[^adr-0010]: ADR 0010: One site playbook, every setting in vars files
