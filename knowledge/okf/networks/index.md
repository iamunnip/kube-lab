# Network

* [Private network](private-network.md) - 192.168.56.0/24 host-only network. Nodes and your computer talk here. Kubernetes uses it.
* [NAT network](nat.md) - 10.0.2.0/24, one per VM, for internet access only.
* [Service routing](service-routing.md) - Cilium eBPF handles Services. No kube-proxy.
* [Cluster address ranges](cluster-ranges.md) - Pod and service ranges for kubeadm and RKE2, and which ranges to avoid.
