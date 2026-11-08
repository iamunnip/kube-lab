# Architecture Decision Records

- One file per decision, with the reason behind it.
- To add one, copy [template.md](template.md) to the next number.
- How ADRs work: [How these docs work](../diataxis/explanation/how-these-docs-work.md).

| ADR                                                         | Decision                                              | Status   |
|-------------------------------------------------------------|-------------------------------------------------------|----------|
| [0001](0001-use-vagrant-and-virtualbox.md)                  | Use Vagrant and VirtualBox for the VMs                | Accepted |
| [0002](0002-use-ubuntu-26-04.md)                            | Use Ubuntu 26.04 LTS from the cloud-image box         | Accepted |
| [0003](0003-docs-with-diataxis-adr-and-okf.md)              | Docs in `docs/` (Diataxis, ADR), facts in `knowledge/okf/` | Accepted |
| [0004](0004-keep-vms-and-provisioning-separate.md)          | Vagrant only makes VMs, Ansible installs everything   | Accepted |
| [0005](0005-node-sizes-and-ips.md)                          | Node names, 3 CPU and 6 GB each, fixed IPs            | Accepted |
| [0006](0006-two-disks-per-node.md)                          | The box's 10 GB OS disk (not resized) and a 20 GB data disk | Accepted |
| [0007](0007-one-folder-per-cluster-layout.md)               | One folder and Vagrantfile per cluster layout         | Accepted |
| [0008](0008-prereq-and-k8s-prepare-roles.md)                | Ansible roles prereq and k8s_prepare, LVM data disk at /data | Accepted |
| [0009](0009-kubeadm-with-containerd-and-cilium.md)          | kubeadm with containerd, Cilium with Helm, one playbook per distribution | Accepted |
| [0010](0010-one-site-playbook-driven-by-vars.md)            | One `site.yml`, every setting in vars files           | Accepted |
| [0011](0011-run-helm-on-control-plane.md)                   | Run Helm and Cilium management on the control plane over SSH | Accepted |
| [0012](0012-cilium-replaces-kube-proxy.md)                  | Cilium eBPF replaces kube-proxy                       | Accepted |
| [0013](0013-short-lived-join-tokens.md)                     | Short lived join tokens, made per node by Ansible     | Accepted |
| [0014](0014-config-files-in-the-roles.md)            | kubeadm, Cilium and RKE2 config files in the roles, vars hold only values | Accepted |
