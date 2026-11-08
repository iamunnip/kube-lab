# kube-lab
A local multi-node Kubernetes lab built with Vagrant for hands-on learning, cluster provisioning, and infrastructure experimentation.

## Getting started

New here? Open [docs/README.md](docs/README.md).

- `vagrant/`: one folder per cluster layout, each with its own `Vagrantfile`.
- `docs/diataxis/`: tutorials, how-to guides, explanation and reference.
- `docs/adr/`: why each choice was made.
- `knowledge/okf/`: short fact pages in Open Knowledge Format.
- `ansible/`: installs and upgrades the cluster (kubeadm or RKE2, with Cilium and no kube-proxy).

## Quick start

```bash
cd vagrant/single-control-plane && vagrant up      # make the 3 VMs
cd ../../ansible && ansible-playbook site.yml      # install Kubernetes
ansible-playbook verify-network.yml                # check pods, DNS and Services
```
