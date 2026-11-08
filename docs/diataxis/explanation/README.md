# Explanation

- Background and the "why". No step by step commands.
- Suggested reading order is top to bottom.

| Page                                                    | Explains                                                  |
|---------------------------------------------------------|-----------------------------------------------------------|
| [How the lab works](how-the-lab-works.md)               | The layers, which tool does what, and how the lab is built |
| [Kubernetes basics](kubernetes-basics.md)               | What Kubernetes is and what runs on each node             |
| [Cluster layouts](cluster-layouts.md)                   | Why each layout has its own folder, and the planned HA layout with kube-vip |
| [The Vagrantfile explained](vagrantfile-explained.md)   | Every line of the `Vagrantfile`, and how to change it     |
| [Lab networking](lab-networking.md)                     | The two network cards and the address ranges              |
| [Disks and storage](disks-and-storage.md)               | The OS disk and the data disk, where they live, what deletes them |
| [The Ansible roles explained](ansible-roles-explained.md) | Ansible words, the `ansible/` folder, and every step of `prereq` and `k8s_prepare` |
| [Installing Kubernetes explained](installing-kubernetes-explained.md) | The settings files, `site.yml`, `reset.yml`, the kubeadm and rke2 roles, upgrades and Cilium |
| [Joining nodes explained](joining-nodes-explained.md)   | How kubeadm join works, the short lived token and the CA hash |
| [Services without kube-proxy](services-without-kube-proxy.md) | What kube-proxy does, and how Cilium does it instead |
| [kubeadm vs RKE2](kubeadm-vs-rke2.md)                   | How the two distributions differ, and which to use        |
| [How these docs work](how-these-docs-work.md)           | Diataxis, ADR and OKF, and the rules for writing pages    |
