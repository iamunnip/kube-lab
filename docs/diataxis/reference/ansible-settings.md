# Ansible settings

- Every setting the lab uses. All of them are in `ansible/inventories/single-control-plane/`.
- The roles and templates have no fixed values: they only read these settings.
- Each table lists the top level settings of one file, with their value today and the comment from the file.
- Change a setting in its file, then run `ansible-playbook site.yml`.

# hosts.ini

- The inventory: which VMs exist and which group each one is in.

| Group | Hosts | Used for |
|-------|-------|----------|
| `control_plane` | `control-plane` | `kubeadm init` or the RKE2 server, Helm and Cilium |
| `nodes` | `node-1`, `node-2` | `kubeadm join` or the RKE2 agent |
| `cluster` | all of the above | Every VM (prepare, reset) |

| Host setting | Example | Meaning |
|--------------|---------|---------|
| `ansible_host` | `192.168.56.11` | The VM's private IP. Ansible connects to it over SSH. |
| `vagrant_machine` | `node-1` | The VM's name in the Vagrantfile. `lab.yml` uses it to find the SSH key Vagrant made for this VM. |

- Why `vagrant_machine` is a host setting and not `inventory_hostname`:
  - Some tasks run on a node but do one step on the control plane (`delegate_to`). The join token is made that way.
  - During such a step `inventory_hostname` still says `node-1`, so Ansible would log in to the control plane with node-1's key and fail with `UNREACHABLE`.
  - `vagrant_machine` is read from the host Ansible really connects to, so the right key is always used.

# cluster.yml

- What to install: distribution, versions, API address, ranges.

| Setting | Value | Meaning |
|---------|-------|---------|
| `k8s_distro` | `kubeadm` | kubeadm plain upstream Kubernetes, built step by step rke2 SUSE Rancher's security focused Kubernetes |
| `kubernetes_version` | `"1.37.2"` | kubeadm: the Kubernetes version. Format "1.37.2". Upgrades: one minor version at a time (1.37.x to 1.38.x), never down. |
| `rke2_version` | `"v1.37.2+rke2r1"` | RKE2: the RKE2 release. Format "v1.37.2+rke2r1". Releases: https://github.com/rancher/rke2/releases |
| `cilium_version` | `"1.20.4"` | Cilium, the pod network, used with both distributions. Releases: https://github.com/cilium/cilium/releases |
| `api_server_address` | `192.168.56.10` | The address kubectl and the nodes use to reach the Kubernetes API. One control plane here, so it is control-plane's private IP. |
| `api_server_port` | `6443` | |
| `pod_subnet` | `10.244.0.0/16` | Address ranges inside the cluster, the same for both distributions. They must not overlap the VM networks (192.168.56.0/24 and 10.0.2.0/24). |
| `service_subnet` | `10.96.0.0/12` | |
| `cluster_name` | `single-control-plane` | The cluster's name. Also the kubeconfig file name on your computer. |

# kubeadm.yml

- containerd, packages, join tokens and the kubeadm config values. The kubeadm config files themselves are in `roles/kubeadm/templates/`.

| Setting | Value | Meaning |
|---------|-------|---------|
| `kubernetes_minor_version` | `"{{ kubernetes_version.split('.')[0:2] \| join('.') }}"` | The minor version, worked out from kubernetes_version: "1.37.2" -> "1.37". |
| `kubernetes_apt_repo_url` | `"https://pkgs.k8s.io/core:/stable:/v{{ kubernetes_minor_version }}/deb/"` | The official Kubernetes package repository for that minor version. |
| `kubernetes_apt_key_url` | `"https://pkgs.k8s.io/core:/stable:/v{{ kubernetes_minor_version }}/deb/Release.key"` | |
| `kubernetes_packages` | (block, 3 lines, see the file) | The packages installed at kubernetes_version, and held there. |
| `containerd_package` | `containerd` | Ubuntu's containerd package. |
| `containerd_root_dir` | `"{{ k8s_containerd_dir }}"` | Where containerd keeps images and containers (on the data disk). |
| `containerd_systemd_cgroup` | `true` | Use the systemd cgroup driver. Must match kubeadm_cgroup_driver below. |
| `containerd_socket` | `unix:///run/containerd/containerd.sock` | Where the kubelet, kubeadm and crictl talk to containerd. |
| `kubeadm_cgroup_driver` | `systemd` | The kubelet cgroup driver. Must match containerd_systemd_cgroup above. |
| `kubeadm_cert_sans` | (block, 2 lines, see the file) | Extra names and IPs the API server certificate is valid for. |
| `kubeadm_kube_proxy_disabled` | `true` | true: kubeadm does not install kube-proxy, because Cilium does its job. Must match kubeProxyReplacement in cilium.yml. |
| `kubeadm_init_token_ttl` | `15m` | kubeadm init always makes one token. kube-lab never uses it (each node gets its own, below), so it lives only a short time and then Kubernetes deletes it. |
| `kubeadm_init_token_description` | `kube-lab init token, not used` | |
| `kubeadm_join_token_ttl` | `10m` | The token made for each node right before it joins. The kubeadm role deletes it right after the join, so this is only a safety limit. The node name is added at the end of the description. |
| `kubeadm_join_token_description` | `kube-lab join token for` | |

# rke2.yml

- RKE2 switches and config values. The RKE2 config files themselves are in `roles/rke2/templates/`.

| Setting | Value | Meaning |
|---------|-------|---------|
| `rke2_ingress_controller` | `none` | Ingress controller RKE2 deploys for you: none no ingress controller (install your own later) traefik RKE2's default ingress-nginx older controller, for migrations only |
| `rke2_enable_servicelb` | `false` | RKE2's built in service load balancer (gives LoadBalancer services an IP on the nodes). RKE2 has it off by default. |
| `rke2_disable` | `[]` | Packaged RKE2 components to leave out. Empty list means: install them all. Allowed names: rke2-coredns, rke2-metrics-server, rke2-snapshot-controller, rke2-snapshot-controller-crd, rke2-snapshot-validation-webhook, rke2-security-responder, rke2-gateway-api-crd |
| `rke2_cni` | `none` | Built in pod network. "none" because Cilium is installed with Helm by the cilium role. Other choices: canal (RKE2's default), calico, cilium, flannel. |
| `rke2_arch` | `amd64` | The CPU type of the VMs. The lab only runs on amd64 (x86_64). |
| `rke2_download_url` | `"https://github.com/rancher/rke2/releases/download/{{ rke2_version \| urlencode }}/rke2.linux-{{ rke2_arch }}.tar.gz"` | The RKE2 tarball for rke2_version, from the RKE2 releases on GitHub. urlencode turns the "+" in "v1.37.2+rke2r1" into "%2B", as GitHub needs. |
| `rke2_tarball_path` | `"{{ k8s_rancher_dir }}/rke2.linux-{{ rke2_arch }}.tar.gz"` | Where the tarball is saved for a moment, on the data disk. |
| `rke2_install_dir` | `/usr/local` | Where RKE2 is unpacked. rke2 lands in <this>/bin, its services in <this>/lib/systemd/system. |
| `rke2_data_dir` | `"{{ k8s_rancher_dir }}/rke2"` | RKE2 keeps everything (binaries, images, containers, etcd) here, on the data disk. |
| `rke2_start_timeout` | `600` | How long to wait for the first RKE2 server start (it writes the join token), in seconds. |
| `rke2_supervisor_port` | `9345` | Port the agents use to join the server (the Kubernetes API stays on api_server_port). |
| `rke2_write_kubeconfig_mode` | `"0600"` | Who can read the admin kubeconfig RKE2 writes on the server. |
| `rke2_tls_san` | (block, 2 lines, see the file) | Extra names and IPs the API server certificate is valid for. |
| `rke2_kubelet_args` | (block, 1 line, see the file) | Extra kubelet flags on every node. |
| `rke2_disable_kube_proxy` | `true` | kube-proxy: true means RKE2 does not start it, because Cilium does its job. Must match cilium_kube_proxy_replacement in cilium.yml. |

# cilium.yml

- The Cilium Helm chart, Helm itself and the Cilium values. The values file itself is `roles/cilium/templates/values.yaml.j2`.

| Setting | Value | Meaning |
|---------|-------|---------|
| `cilium_helm_repo_name` | `cilium` | |
| `cilium_helm_repo_url` | `https://helm.cilium.io/` | |
| `cilium_chart` | `"{{ cilium_helm_repo_name }}/cilium"` | |
| `cilium_release_name` | `cilium` | |
| `cilium_namespace` | `kube-system` | |
| `cilium_wait_timeout` | `10m` | How long to wait for Cilium, the nodes and the system apps to be ready. |
| `cilium_helm_dir` | `"{{ data_mount_point }}/helm"` | Helm files and kubeconfig on the control plane. |
| `cilium_values_file` | `"{{ cilium_helm_dir }}/cilium-values.yaml"` | |
| `cilium_kubeconfig_path` | `"/home/{{ vm_kubectl_user }}/.kube/config"` | |
| `cilium_helm_binary` | `"{{ cilium_helm_dir }}/bin/helm"` | |
| `helm_version` | `v4.3.1` | |
| `cilium_kube_proxy_replacement` | `true` | true: Cilium does kube-proxy's job, and kube-proxy is not installed. Must match kubeadm_kube_proxy_disabled (kubeadm.yml) and disable-kube-proxy (rke2.yml). |
| `cilium_ipam_mode` | `kubernetes` | How pods get their IPs. "kubernetes": from pod_subnet in cluster.yml. Do not use Cilium's default "cluster-pool" (10.0.0.0/8 overlaps the NAT network). |
| `cilium_operator_replicas` | `1` | How many Cilium operators run. One is enough for a small lab (the default is 2). |

# os.yml

- OS upgrade, GRUB, data disk LVM, kernel settings.

| Setting | Value | Meaning |
|---------|-------|---------|
| `os_upgrade_type` | `dist` | "dist" also installs new kernels and new package dependencies. Other choices: "safe", "full", "yes" (see the apt module docs). |
| `os_upgrade_autoremove` | `true` | Remove packages nothing needs any more after the upgrade. |
| `os_reboot_if_needed` | `true` | Reboot when Ubuntu says an update needs it (for example a new kernel). |
| `os_reboot_timeout` | `600` | How long to wait for a VM to come back after a reboot, in seconds. |
| `apt_lock_timeout` | `600` | How long apt waits if Ubuntu's own updater is busy, in seconds. |
| `ssh_wait_timeout` | `300` | How long the playbooks wait for SSH after "vagrant up", in seconds. |
| `grub_masked_services` | (block, 2 lines, see the file) | On this box GRUB breaks /boot/grub/grubenv at every boot, so these two services always fail. They are masked (switched off). Empty list: do nothing. |
| `data_vg_name` | `data_vg` | LVM volume group |
| `data_lv_name` | `data_lv` | LVM logical volume |
| `data_lv_size` | `100%FREE` | how much of the volume group the LV uses |
| `data_fstype` | `ext4` | filesystem on the logical volume |
| `data_mount_options` | `defaults,nofail` | nofail: the VM still boots if the disk is missing |
| `kernel_modules` | (block, 2 lines, see the file) | Kernel modules loaded now and at every boot. |
| `kernel_modules_file` | `/etc/modules-load.d/kubernetes.conf` | |
| `kernel_sysctls` | (block, 3 lines, see the file) | Kernel settings (sysctls) set now and at every boot. |
| `kernel_sysctl_file` | `/etc/sysctl.d/99-kubernetes.conf` | |

# lab.yml

- Login, data disk, /data folders, file locations, download retries.

| Setting | Value | Meaning |
|---------|-------|---------|
| `ansible_user` | `vagrant` | The user Vagrant creates in every VM. |
| `lab_login_shell` | `/bin/bash` | Interactive SSH sessions need Bash for command history and arrow keys. |
| `ansible_ssh_private_key_file` | `"{{ playbook_dir }}/../vagrant/single-control-plane/.vagrant/machines/{{ vagrant_machine }}/virtualbox/private_key"` | The SSH key Vagrant made for each VM. playbook_dir is the ansible/ folder vagrant_machine is the VM name, set per host in hosts.ini, such as "node-1" Do not use inventory_hostname here: with delegate_to it keeps the name of the original VM, so Ansible would log in to the control plane with node-1's key. |
| `vm_kubectl_user` | `"{{ ansible_user }}"` | The user on the control plane that gets a kubeconfig in ~/.kube/config. |
| `data_disk_device` | `/dev/sdb` | The empty 20 GB disk from the Vagrantfile. Seen as /dev/sdb on every VM. |
| `data_mount_point` | `/data` | Where the data disk is mounted. |
| `k8s_containerd_dir` | `"{{ data_mount_point }}/containerd"` | kubeadm: containerd images and containers |
| `k8s_etcd_dir` | `"{{ data_mount_point }}/etcd"` | kubeadm: the etcd database (kubeadm makes it) |
| `k8s_rancher_dir` | `"{{ data_mount_point }}/rancher"` | RKE2: rke2_data_dir is inside it |
| `k8s_kubelet_dir` | `"{{ data_mount_point }}/kubelet"` | both: kubelet data and pod volumes |
| `k8s_storage_dir` | `"{{ data_mount_point }}/storage"` | both: app volumes (local storage) later |
| `k8s_data_dirs` | (block, 4 lines, see the file) | The folders k8s_prepare makes. /data/etcd is NOT in this list: kubeadm makes it with the strict permissions (0700) etcd demands. |
| `kube_lab_config_dir` | `/etc/kube-lab` | Folder for the distro marker and the kubeadm config files. |
| `kubeconfig_local_path` | `"{{ playbook_dir }}/../.kube/{{ cluster_name }}.conf"` | The admin kubeconfig, so you can run kubectl and helm on your computer. The .kube/ folder is in .gitignore. |
| `download_timeout` | `60` | How long to wait for an answer, in seconds. |
| `download_retries` | `5` | How many times to try again after a failure, 30 seconds apart. |

# Fixed paths kept in the roles

- These are decided by the tools themselves. Changing them would not change where the tools read or write, so they are not settings.

| Role | Setting | Value | Why it is fixed |
|------|---------|-------|-----------------|
| rke2 | `rke2_config_dir` | `/etc/rancher/rke2` | RKE2 always reads `config.yaml` here |
| rke2 | `rke2_kubeconfig_path` | `/etc/rancher/rke2/rke2.yaml` | RKE2 always writes the admin kubeconfig here |
| rke2 | `rke2_bin_dir` | `/usr/local/bin` | The RKE2 tarball has `rke2` and its scripts in `bin/`, unpacked into `/usr/local` |
| kubeadm | `templates/kubeadm-init.yaml.j2`, `templates/kubeadm-join.yaml.j2` | the kubeadm config files | The real kubeadm YAML. Only values come from vars ([ADR 0014](../../adr/0014-config-files-in-the-roles.md)) |
| rke2 | `templates/config-server.yaml.j2`, `templates/config-agent.yaml.j2` | the RKE2 config files | The real RKE2 YAML. Only values come from vars |
| cilium | `templates/values.yaml.j2` | the Cilium Helm values file | The real Helm values. Only values come from vars |
| kubeadm | `kubeadm_ca_cert_path` | `/etc/kubernetes/pki/ca.crt` | kubeadm always writes the cluster CA here. The join CA hash is worked out from it |
| kubeadm | (in tasks) | `/etc/kubernetes/admin.conf` | kubeadm always writes the admin kubeconfig here |
| cilium | `lab_helm_env` | from `cilium_helm_dir` | Worked out from `cilium_helm_dir`, not a setting of its own |

# Tags

| Tag | Role | Runs |
|-----|------|------|
| `grub` | prereq | Mask the GRUB services in `grub_masked_services` |
| `os_upgrade` | prereq | apt update, upgrade, reboot if needed |
| `data_disk` | prereq | LVM, filesystem, mount `/data`, fstab |
| `data_dirs` | k8s_prepare | Folders in `k8s_data_dirs` |
| `swap` | k8s_prepare | Swap off |
| `kernel` | k8s_prepare | Kernel modules and sysctls |
| `hosts` | k8s_prepare | `/etc/hosts` entries |
| `containerd` | kubeadm | Install and configure containerd |
| `packages` | kubeadm | Install and hold the Kubernetes packages |
| `init` | kubeadm | `kubeadm init`, kubeconfig |
| `join` | kubeadm | Short lived join token, `kubeadm join`, token deleted again |
| `upgrade` | kubeadm | `kubeadm upgrade` when the version is raised |
| `install` | rke2 | Read the installed version, download and unpack the RKE2 tarball when needed |
| `server` | rke2 | RKE2 server on the control plane |
| `agent` | rke2 | RKE2 agent on the nodes |
| `marker` | kubeadm, rke2 | Write `/etc/kube-lab/distro` |

- Run one part: `ansible-playbook site.yml --tags kernel`.
- Skip one part: `ansible-playbook site.yml --skip-tags os_upgrade`.

# Files the lab writes on each VM

| File | Written by | Holds |
|------|-----------|-------|
| `/etc/fstab` | prereq | The `/data` mount line |
| `kernel_modules_file` (`/etc/modules-load.d/kubernetes.conf`) | k8s_prepare | Kernel modules to load at boot |
| `kernel_sysctl_file` (`/etc/sysctl.d/99-kubernetes.conf`) | k8s_prepare | Kernel settings |
| `/etc/hosts` | k8s_prepare | The `kube-lab nodes` block |
| `/etc/containerd/config.toml` | kubeadm | containerd config |
| `/etc/kube-lab/kubeadm-init.yaml` | kubeadm | The kubeadm init config (control plane). No token in it: kubeadm makes a random one |
| `/etc/kube-lab/kubeadm-join.yaml` | kubeadm | The join config with the join token. Only exists during the join, then deleted |
| `/etc/rancher/rke2/config.yaml` | rke2 | The RKE2 server or agent config |
| `/etc/kube-lab/distro` | kubeadm, rke2 | Which distribution is installed |
| `/home/<vm_kubectl_user>/.kube/config` | kubeadm, rke2 | kubectl access on the control plane |
| `cilium_values_file` (`/data/helm/cilium-values.yaml`) | cilium | The Cilium values Helm uses (control plane) |
| `/data/kube-proxy-before-cilium-iptables.rules` | site.yml step 4 | Backup of old kube-proxy rules. Only when an old cluster had kube-proxy |

# Files the lab writes on your computer

| File | Written by | Holds |
|------|-----------|-------|
| `kubeconfig_local_path` (`kube-lab/.kube/single-control-plane.conf`) | kubeadm, rke2 | Admin kubeconfig. Secret, not in git |

# Secrets and how they are kept safe

| Secret | Where it lives | How long |
|--------|----------------|----------|
| kubeadm init token | Kubernetes only, never in a file | `kubeadm_init_token_ttl` (15 minutes), then Kubernetes deletes it |
| kubeadm join token | Kubernetes and `kubeadm-join.yaml` on the joining node | Deleted right after the join, even when the join fails. At most `kubeadm_join_token_ttl` (10 minutes) |
| RKE2 token | `/data/rancher/rke2/server/node-token` on the server, `/etc/rancher/rke2/config.yaml` on the agents | As long as the cluster lives (RKE2's own design) |
| Admin kubeconfig | Control plane and `kube-lab/.kube/` | As long as the cluster lives. Mode `0600` |

- Tasks that handle the kubeadm join token have `no_log: true`, so Ansible never prints it.
- The `kubeadm init` output is shown without the lines that hold a token.
