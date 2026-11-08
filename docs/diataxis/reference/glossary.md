# Glossary

- Words used in these docs, in plain English, sorted A to Z.

# A to C

| Word                | Meaning                                                                 |
|---------------------|-------------------------------------------------------------------------|
| ADR                 | Architecture Decision Record. A short note that records one decision and why. See `docs/adr/`. |
| Agent               | In RKE2, a node that runs apps and joins a server. In this lab: `node-1` and `node-2`. |
| Ansible             | A tool that logs into computers over SSH and sets them up from files called playbooks. |
| API server          | `kube-apiserver`. The front door of Kubernetes. Every request goes through it. |
| Bootstrap token     | The kubeadm name for a join token: a short password like `abcdef.0123456789abcdef`, with a TTL. kube-lab makes one per node and deletes it after the join. |
| Box                 | A ready made VM disk image for Vagrant, with an OS already installed. This lab uses `cloud-image/ubuntu-26.04`. |
| CA hash             | A fingerprint of the cluster's certificate authority. `kubeadm join` checks it to be sure it talks to the real control plane. |
| Cgroup driver       | How the kubelet and containerd group processes to limit CPU and memory. Both must use the same one: `systemd` here. |
| Chart (Helm)        | A Helm package: everything needed to install one app on Kubernetes, such as Cilium. |
| CIDR                | A short way to write a range of IP addresses. `192.168.56.0/24` means `192.168.56.0` to `192.168.56.255`. |
| Cilium              | The pod network (CNI) in this lab. Gives pods IPs and connects them across nodes. Installed with Helm. |
| ClusterIP           | The fixed virtual IP of a Service, from `service_subnet` (`10.96.0.0/12`). Only works inside the cluster. |
| Cloud image         | An official, ready to boot OS disk image made for clouds and VMs. The lab box is built from the Ubuntu 26.04 cloud image. |
| cloud-init          | A tool inside cloud images that sets things up on first boot, such as growing the main partition to fill the disk. |
| Cluster             | A group of nodes managed by Kubernetes as one system.                  |
| CNI                 | Container Network Interface. The plugin that gives pods IP addresses and connects them. Examples: Flannel, Calico, Cilium. |
| Collection (Ansible) | A package of extra Ansible modules, such as `kubernetes.core`. Listed in `ansible/requirements.yml`. |
| Container           | A packaged app with everything it needs to run, isolated from other apps. |
| Container runtime   | The program that starts and stops containers. This lab uses containerd. |
| Control plane       | The part of Kubernetes that manages the cluster. In this lab, the VM named `control-plane`. |
| CoreDNS             | The DNS server inside the cluster. Lets pods find services by name, such as `web.nettest.svc.cluster.local`. |
| crictl              | A debug tool on the VMs to list containers and images straight from containerd: `sudo crictl ps`. |

# D to K

| Word                | Meaning                                                                 |
|---------------------|-------------------------------------------------------------------------|
| Data disk           | The second, empty 20 GB disk in each VM. Not formatted or mounted. For storage practice later. |
| Deployment          | A Kubernetes object that keeps a set number of identical pods running. |
| Desired state       | What you told Kubernetes should be running. Kubernetes keeps making it true. |
| Diataxis            | A way to sort docs into tutorials, how-to guides, explanation and reference. |
| Disk (Vagrant)      | A virtual hard disk set with `vm.disk` in the `Vagrantfile`. Each lab VM has 2. |
| Distribution        | A packaged way to install Kubernetes. This lab has kubeadm and RKE2.    |
| Distro marker       | The file `/etc/kube-lab/distro` that says which distribution is installed on a VM. |
| delegate_to         | An Ansible keyword: run this one task on another machine. The join token is made on the control plane this way, while Ansible works on a node. |
| Dynamically allocated | A disk file that starts small and grows as data is written, up to its set size. |
| eBPF                | Small, safe programs that run inside the Linux kernel. Cilium uses them for pod traffic and Services. |
| etcd                | The database where Kubernetes stores the state of the cluster.         |
| Frontmatter         | The header at the top of an OKF page, between two `---` lines.         |
| fstab               | `/etc/fstab`, the file that lists which disks to mount at boot, and where. |
| Group vars          | Ansible settings for every machine in a group. Here: `group_vars/all/`. |
| Guest               | The VM. The opposite of host.                                          |
| Handler             | An Ansible task that runs once at the end, only if another task asked for it with `notify`. |
| Helm                | The package manager for Kubernetes. Installs charts with settings from a values file. |
| Host                | Your own computer, the one that runs VirtualBox.                       |
| Host-only adapter   | The VirtualBox network card type used for the private network. Same as host-only network. |
| Host-only network   | A VirtualBox network that only the VMs and your computer can use. Our private network. |
| Hypervisor          | Software that runs VMs. VirtualBox and KVM are hypervisors.            |
| Idempotent          | Safe to run again: if everything is already done, nothing changes (`changed=0`). |
| Ingress             | A Kubernetes way to send web traffic from outside into the right service. |
| Inventory           | Ansible's list of computers to manage, with their addresses.           |
| IO-APIC             | Part of a computer's hardware that spreads work across CPUs. A VM needs it turned on to use more than 1 CPU. |
| Join token          | A secret a node gives the control plane to prove it may join the cluster. For kubeadm: a bootstrap token. |
| kubeadm             | The official Kubernetes tool to build a cluster step by step.          |
| kubeadm init        | The command that creates a new cluster on the first control plane. |
| kubeadm join        | The command that adds a node to an existing cluster, using a token and a CA hash. |
| kube-proxy          | The usual program that sends Service traffic to pods, with iptables rules. Not used in this lab: Cilium does its job. |
| kube-proxy replacement | Cilium's mode that does kube-proxy's job with eBPF. Turned on with `cilium_kube_proxy_replacement: true` in `cilium.yml`. |
| kubeconfig          | A file that tells kubectl where the cluster is and how to log in.      |
| kubectl             | The command line tool to talk to Kubernetes.                           |
| kubelet             | The agent on every node that starts the pods the API server asks for.  |
| KVM                 | The hypervisor built into Linux. Can block VirtualBox on some setups.  |

# L to R

| Word                | Meaning                                                                 |
|---------------------|-------------------------------------------------------------------------|
| Logical volume (LV) | An LVM slice you format and mount, like a partition. Here: `data_lv`, mounted at `/data`. |
| LTS                 | Long Term Support. An OS release that gets updates for many years.     |
| LVM                 | Logical Volume Manager. A layer between disks and filesystems that makes storage easy to grow. |
| Mask (systemd)      | Switch a service off so systemd never starts it. Undo with `systemctl unmask`. |
| Namespace           | A folder inside Kubernetes to keep objects apart.                      |
| NAT                 | Network Address Translation. Lets a VM reach the internet through your computer. Card 1 in each VM. |
| no_log              | An Ansible task option that hides the task's input and output from the log. Used for the join token. |
| Node                | One computer (here, one VM) in a Kubernetes cluster. In this lab, `node-1` and `node-2` are the nodes that run your apps. |
| Node IP             | The address a node tells the rest of the cluster to use. Must be the `192.168.56.x` address. |
| OKF                 | Open Knowledge Format. Plain markdown pages with a small header, one page per thing. See `knowledge/okf/`. |
| OS disk             | The first disk in each VM, 10 GB, from the box. Ubuntu boots from it. |
| Physical volume (PV)| A real disk given to LVM. Here: `/dev/sdb`. |
| Playbook            | An Ansible file that lists what to do, in order, on which computers.   |
| Pod                 | The smallest thing Kubernetes runs: one or more containers that share an IP. |
| Private network     | The `192.168.56.0/24` network shared by the VMs and your computer. Card 2 in each VM. |
| Provider            | What Vagrant uses to run VMs. In this lab: VirtualBox.                 |
| Provisioning        | Installing and setting up software on a machine. In this lab Ansible does it, not Vagrant. |
| RKE2                | A security focused Kubernetes distribution from SUSE Rancher.          |
| Role                | A reusable group of Ansible tasks, such as "install RKE2".             |

# S to Z

| Word                | Meaning                                                                 |
|---------------------|-------------------------------------------------------------------------|
| Server              | In RKE2, the node that runs the control plane. In this lab: `control-plane`. |
| Service             | A stable name and IP in front of a group of pods.                      |
| SSH                 | Secure Shell. A safe way to log into another computer from a terminal. |
| SSH key             | A pair of files used to log in over SSH without a password.            |
| Swap                | Disk space used as extra memory. Kubernetes nodes usually have it turned off. |
| Tag (Ansible)       | A label on tasks, so you can run only some of them with `--tags`. |
| TTL                 | Time to live. How long something stays valid before it is deleted, such as `10m` for a join token. |
| Vagrant             | A tool that creates and controls VMs from a `Vagrantfile`.             |
| Vagrantfile         | The file that describes the lab VMs. Written in Ruby.                  |
| Values file         | A YAML file with Helm settings for one chart. Here: `/data/helm/cilium-values.yaml` on the control plane, written from the role's `values.yaml.j2` with values from `cilium.yml`. |
| VirtualBox          | The program that runs the lab VMs on your computer.                    |
| VM                  | Virtual machine. A computer made in software, running inside your real computer. |
| Volume group (VG)   | An LVM pool made from physical volumes. Here: `data_vg`. |
| VT-x and AMD-V      | CPU features that make VMs fast. Must be turned on in the BIOS.        |
