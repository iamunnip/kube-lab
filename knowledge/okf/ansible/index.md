# Ansible Role

* [prereq](prereq.md) - OS level basics: switch off 2 failing GRUB services, OS upgrade, LVM data disk at /data.
* [k8s_prepare](k8s-prepare.md) - Kubernetes level basics: /data folders, swap off, kernel modules and settings, /etc/hosts.
* [inventory](inventory.md) - hosts.ini: groups, IPs and vagrant_machine for the SSH keys.
* [kubeadm](kubeadm.md) - containerd, kubeadm packages, kubeadm init and join with a short lived token, no kube-proxy, distro marker.
* [rke2](rke2.md) - RKE2 server and agents, upgrade by version, reset.
* [cilium](cilium.md) - Cilium pod network with Helm on the control plane, kube-proxy replacement, wait for Ready nodes.
