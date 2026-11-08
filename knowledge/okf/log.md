# Bundle update log

## 2026-11-08
* **Update**: The [rke2](ansible/rke2.md) role installs RKE2 from the release tarball only (no install script, no checksum file), for amd64 (`rke2_arch`). Downloads retry (`download_retries` in `lab.yml`).
* **Update**: The [rke2](ansible/rke2.md) config files now live in the role's templates. `rke2.yml` holds only values.
* **Update**: The [cilium](ansible/cilium.md) role only installs Helm in `/data/helm` and runs the install. Removed helm-diff, the key and checksum checks and the kubeconfig copy.
* **Update**: The kubeadm config files (`kind: InitConfiguration` and the others) now live in the [kubeadm](ansible/kubeadm.md) role's templates, and the Cilium Helm values in the [cilium](ansible/cilium.md) role's `values.yaml.j2`. `kubeadm.yml` and `cilium.yml` hold only values.
* **Update**: kubeadm join now makes a short lived token per node (`--ttl 10m`), works out the CA hash with `openssl` and always deletes the token afterwards. The init token is `ttl: 15m`. See [kubeadm](ansible/kubeadm.md).
* **Update**: Fixed SSH under `delegate_to`: hosts in `hosts.ini` have `vagrant_machine`, used for the key path. Added the [inventory](ansible/inventory.md) page.
* **Creation**: Added [service routing](networks/service-routing.md): Cilium replaces kube-proxy. Updated [cilium](ansible/cilium.md): Helm runs on the control plane.
* **Update**: [Ansible](tools/ansible.md) lists all playbooks, roles and `requirements.yml`.
* **Update**: Enabled Cilium eBPF kube-proxy replacement, disabled kube-proxy in kubeadm and RKE2 configurations, and added a cross-node networking verification playbook.
* **Update**: Cilium management now runs over SSH on the control plane, with Helm installed by Ansible.
* **Update**: Increased all three nodes to 6 GB memory (6144 MB) each; total VM memory is now 18 GB.
* **Update**: One `site.yml` and `reset.yml`, chosen by `k8s_distro`. Every setting moved to `group_vars/all/` files. Added the [rke2](ansible/rke2.md) role and updated [RKE2](distributions/rke2.md).
* **Creation**: Built the cluster with the [kubeadm](ansible/kubeadm.md) and [cilium](ansible/cilium.md) roles. Kubernetes v1.37.2, containerd 2.2.2, Cilium 1.20.4. Added [Helm](tools/helm.md).
* **Creation**: Added the Ansible roles [prereq](ansible/prereq.md) and [k8s_prepare](ansible/k8s-prepare.md). The [data disk](storage/data-disk.md) is now LVM `data_vg/data_lv`, ext4, mounted at `/data`.
* **Update**: Tested the [single-control-plane](layouts/single-control-plane.md) layout. The OS disk is no longer resized and stays at the box's 10 GB. Added the IO-APIC fix (3 CPUs), a VirtIO private card and the GRUB file repair.
* **Update**: Moved the Vagrantfile to `vagrant/single-control-plane/` and added the [layouts](layouts/index.md) folder. VirtualBox names now start with `kube-lab-single-`.
* **Creation**: Recorded the planned [ha-control-plane](layouts/ha-control-plane.md) layout with kube-vip.
* **Update**: Nodes now have 3 CPUs, 4 GB memory, a 10 GB [OS disk](storage/os-disk.md) and a 20 GB [data disk](storage/data-disk.md). Box changed to `cloud-image/ubuntu-26.04`.
* **Creation**: Added the [storage](storage/index.md) folder.
* **Creation**: Started the bundle with [nodes](nodes/index.md), [networks](networks/index.md), [tools](tools/index.md) and [distributions](distributions/index.md).
* **Update**: Renamed the VMs to [control-plane](nodes/control-plane.md), [node-1](nodes/node-1.md) and [node-2](nodes/node-2.md).
* **Update**: Moved the human docs to `docs/diataxis/` and `docs/adr/`. This bundle now holds facts only.
