---
type: Network
title: Cluster address ranges
description: The pod and service address ranges, the same for kubeadm and RKE2 in this lab, and which ranges must be avoided.
tags: [network, kubernetes, cidr]
---

# Facts

| Range             | Used for   | Distribution                                                         | Set by                        |
|-------------------|------------|----------------------------------------------------------------------|-------------------------------|
| `10.244.0.0/16`   | Pods       | [kubeadm](../distributions/kubeadm.md) and [RKE2](../distributions/rke2.md) | `pod_subnet` in `cluster.yml` |
| `10.96.0.0/12`    | Services   | [kubeadm](../distributions/kubeadm.md) and [RKE2](../distributions/rke2.md) | `service_subnet` in `cluster.yml` |

# Ranges that must not overlap with these

| Range              | Owner                                            |
|--------------------|--------------------------------------------------|
| `192.168.56.0/24`  | [Private network](private-network.md)            |
| `10.0.2.0/24`      | [NAT network](nat.md)                            |

# Rules

- Pod, service and VM ranges must never overlap.
- Do not use Calico's example pod range `192.168.0.0/16`. It contains `192.168.56.0/24`.
- Do not use Cilium's default range `10.0.0.0/8`. It contains `10.0.2.0/24`. The lab sets `ipam.mode: kubernetes` in the [cilium role](../ansible/cilium.md).
- RKE2's own defaults (`10.42.0.0/16`, `10.43.0.0/16`) are not used: the lab sets RKE2 to the same ranges as kubeadm.

# Learn more

- [Lab networking](../../../docs/diataxis/explanation/lab-networking.md)
