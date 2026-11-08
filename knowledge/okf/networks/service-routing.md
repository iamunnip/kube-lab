---
type: Network
title: Service routing without kube-proxy
description: Cilium eBPF handles ClusterIP and NodePort traffic. kube-proxy is never installed, with kubeadm or RKE2. Checked with verify-network.yml.
tags: [network, kubernetes, cilium, kube-proxy, ebpf]
status: stable
sources:
  - id: adr-0012
    resource: ../../../docs/adr/0012-cilium-replaces-kube-proxy.md
    title: "ADR 0012: Cilium replaces kube-proxy"
---

# Facts

| Field                  | Value                                                          |
|------------------------|----------------------------------------------------------------|
| Done by                | [Cilium](../ansible/cilium.md), `cilium_kube_proxy_replacement: true` in `cilium.yml`, used as `kubeProxyReplacement`[^adr-0012] |
| kube-proxy             | Not installed                                                  |
| kubeadm setting        | `kubeadm_kube_proxy_disabled: true`, used as `proxy.disabled` in `kubeadm-init.yaml.j2` |
| RKE2 setting           | `rke2_disable_kube_proxy: true`, used as `disable-kube-proxy` in `config-server.yaml.j2` |
| How Cilium reaches the API | `k8sServiceHost` `192.168.56.10`, `k8sServicePort` `6443`, not `10.96.0.1` |
| Service range          | `10.96.0.0/12`, see [cluster ranges](cluster-ranges.md)        |
| Cilium devices         | `enp0s8` (private network, direct routing) and `enp0s3` (NAT)  |
| Old kube-proxy rules   | Saved to `/data/kube-proxy-before-cilium-<tool>.rules`, then removed by `site.yml` step 4 |
| Check                  | `ansible-playbook verify-network.yml`: pod IP, DNS and ClusterIP, NodePort across nodes |
| Tested                 | 2026-11-08: `KubeProxyReplacement: True`, no kube-proxy DaemonSet, 0 `KUBE-SVC` rules, verify-network `failed=0` |

# Learn more

- [Services without kube-proxy](../../../docs/diataxis/explanation/services-without-kube-proxy.md)
- [Check the network works](../../../docs/diataxis/how-to/verify-the-network.md)

[^adr-0012]: ADR 0012: Cilium replaces kube-proxy
