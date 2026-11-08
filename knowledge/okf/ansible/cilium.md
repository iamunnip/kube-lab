---
type: Ansible Role
title: cilium
description: Installs or upgrades Cilium, the pod network, with Helm on the control plane, using the role's values.yaml.j2 with values from cilium.yml and the lab's own Helm setup in /data/helm. Replaces kube-proxy, then waits for Ready nodes and system apps.
tags: [ansible, role, cilium, helm, cni]
resource: ../../../ansible/roles/cilium
status: stable
sources:
  - id: adr-0009
    resource: ../../../docs/adr/0009-kubeadm-with-containerd-and-cilium.md
    title: "ADR 0009: kubeadm with containerd, Cilium with Helm"
  - id: adr-0011
    resource: ../../../docs/adr/0011-run-helm-on-control-plane.md
    title: "ADR 0011: Run Helm and Cilium management on the control plane over SSH"
  - id: adr-0012
    resource: ../../../docs/adr/0012-cilium-replaces-kube-proxy.md
    title: "ADR 0012: Cilium replaces kube-proxy"
---

# Facts

| Field                | Value                                                         |
|----------------------|---------------------------------------------------------------|
| Folder               | `ansible/roles/cilium/`                                       |
| Runs on              | the first `control_plane` host over SSH[^adr-0011]           |
| Needs on your computer | Only Ansible with `kubernetes.core` (`ansible/requirements.yml`) |
| Helm                 | `helm_version` v4.3.1 in `/data/helm/bin`, no plugins |
| Downloads            | `download_timeout` 60 s, `download_retries` 5               |
| Kubeconfig used      | `/home/vagrant/.kube/config` on the control plane |
| Chart                | `cilium/cilium` at `cilium_version` from `https://helm.cilium.io/` |
| Namespace            | `kube-system`                                                 |
| Values               | `templates/values.yaml.j2` in the role (the real Helm values file), values from `group_vars/all/cilium.yml`, written to `/data/helm/cilium-values.yaml` |
| Key values           | `kubeProxyReplacement: true` (`cilium_kube_proxy_replacement`), `rollOutCiliumPods: true`, `k8sServiceHost`/`k8sServicePort` = API address, `ipam.mode: kubernetes`, `operator.replicas: 1` |
| kube-proxy           | Replaced[^adr-0012]. Each agent must say `KubeProxyReplacement: True` before any old kube-proxy is removed. See [service routing](../networks/service-routing.md) |
| Helm setup           | `HELM_REPOSITORY_CONFIG` and `HELM_REPOSITORY_CACHE` point to `/data/helm/` |
| Used with            | Both [kubeadm](../distributions/kubeadm.md) and [RKE2](../distributions/rke2.md) |
| Tested               | 2026-11-08: `cilium status` OK, `verify-network.yml` `failed=0` (pod IP, DNS and ClusterIP, NodePort across nodes) |

# Learn more

- [Installing Kubernetes explained](../../../docs/diataxis/explanation/installing-kubernetes-explained.md)
- [Services without kube-proxy](../../../docs/diataxis/explanation/services-without-kube-proxy.md)

[^adr-0011]: ADR 0011: Run Helm and Cilium management on the control plane over SSH
[^adr-0012]: ADR 0012: Cilium replaces kube-proxy
