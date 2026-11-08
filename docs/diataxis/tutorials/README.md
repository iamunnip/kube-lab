# Tutorials

- Do these in order. Each one starts where the last one ended.

| # | Tutorial                                         | You will                                         | Time      |
|---|--------------------------------------------------|--------------------------------------------------|-----------|
| 1 | [Install the tools](01-install-tools.md)         | Install VirtualBox, Vagrant, Ansible and git     | 15 min    |
| 2 | [Create the VMs](02-create-vms.md)               | Start `control-plane`, `node-1`, `node-2`, check disks and test the network | 15 to 30 min |
| 3 | [Prepare the nodes](03-prepare-nodes.md)         | Run Ansible: OS upgrade, LVM data disk at `/data`, Kubernetes basics | 10 min |
| 4 | [Install the cluster](04-install-the-cluster.md) | Build Kubernetes with kubeadm and Cilium from `cluster.yml`, test pod networking | 10 min |
| 5 | [Switch to RKE2 and upgrade](05-switch-to-rke2-and-upgrade.md) | Reset, install RKE2, then upgrade it by changing a version | 20 min |
