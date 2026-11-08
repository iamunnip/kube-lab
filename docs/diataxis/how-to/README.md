# How-to guides

- Short steps for one task. They assume you finished the tutorials.

| Guide                                                       | Use it when                                         |
|-------------------------------------------------------------|-----------------------------------------------------|
| [Start, stop and delete VMs](manage-vms.md)                 | You want to pause, restart, rebuild or remove VMs.  |
| [Update the VMs](update-the-vms.md)                         | You want the latest Ubuntu updates on the VMs.      |
| [Change a setting or upgrade](change-settings-and-upgrade.md) | You want a new version, or a different kubeadm, RKE2 or Cilium setting. |
| [Switch between kubeadm and RKE2](switch-distribution.md)   | You want the other distribution on the same VMs.    |
| [Check the network works](verify-the-network.md)           | You want proof that pods, DNS and Services work without kube-proxy. |
| [Rejoin a node](rejoin-a-node.md)                           | One kubeadm node is broken, or you want to join it again. |
| [Fix the VirtualBox and KVM error](fix-virtualbox-kvm-error.md) | `vagrant up` fails with `VERR_VMX_IN_VMX_ROOT_MODE`. |
