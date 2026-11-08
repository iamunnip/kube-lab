# How the lab works

- This page gives the big picture: the layers, which tool does what, and how the lab is built.

# The layers

- The lab is built in layers. Each layer sits on top of the one below it.

```mermaid
flowchart TB
    l5["5. Your apps (pods)<br/>you deploy these with kubectl"]
    l4["4. Kubernetes: kubeadm or RKE2<br/>installed by Ansible"]
    l3["3. Ubuntu 26.04 inside each VM<br/>comes ready made in the Vagrant box"]
    l2["2. VirtualBox VMs: control-plane, node-1, node-2<br/>created by Vagrant"]
    l1["1. Your computer: CPU, RAM, disk, Linux<br/>you already have this"]
    l5 --- l4 --- l3 --- l2 --- l1
```

- **Layer 1, your computer**: lends part of its CPU, memory and disk to the VMs.
- **Layer 2, the VMs**: each VM acts like a separate computer with its own disks and network cards.
- **Layer 3, Ubuntu**: the operating system inside each VM. It comes pre-installed in the Vagrant box, so there is no installer to click through.
- **Layer 4, Kubernetes**: joins the 3 VMs into one cluster that can run apps.
- **Layer 5, your apps**: what you actually want to run, packaged as containers.

# What each VM has

```mermaid
flowchart LR
    subgraph vm["One VM, for example node-1"]
        cpu["3 CPUs"]
        ram["6 GB memory"]
        nic1["Network card 1: NAT<br/>internet access"]
        nic2["Network card 2: private, VirtIO<br/>192.168.56.11"]
        d1["Disk 1: OS, 10 GB<br/>from the box, Ubuntu and Kubernetes"]
        d2["Disk 2: data, 20 GB<br/>empty, for storage later"]
    end
```

- All 3 VMs are the same size. Only the name and the private IP differ.
- Networks: [Lab networking](lab-networking.md). Disks: [Disks and storage](disks-and-storage.md).

# Who does what

| Tool       | Its one job                                 | Runs on                | Reads this file             | When you use it               |
|------------|---------------------------------------------|------------------------|-----------------------------|-------------------------------|
| VirtualBox | Runs the VMs                                | Your computer          | (none, Vagrant drives it)   | All the time, in the background |
| Vagrant    | Creates, starts, stops, deletes VMs         | Your computer          | `Vagrantfile`               | Building or removing the lab  |
| Ansible    | Installs and configures software on the VMs | Your computer          | `ansible/` folder (coming next) | Installing Kubernetes     |
| kubectl    | Talks to Kubernetes                         | Your computer or `control-plane` | `kubeconfig`      | Using the cluster             |

- Ansible and kubectl run on **your computer**, not inside the VMs.
- They reach the VMs over the private network. See [Lab networking](lab-networking.md).

# How the lab is built: 3 phases

```mermaid
flowchart LR
    p1["Phase 1: make the VMs<br/>vagrant up"]
    r1["3 empty Ubuntu VMs<br/>that can ping each other"]
    p2["Phase 2: prepare, then install Kubernetes<br/>ansible-playbook site.yml<br/>(kubeadm or RKE2, from cluster.yml)"]
    r2["3 VMs joined into<br/>one Kubernetes cluster"]
    p3["Phase 3: use the cluster<br/>kubectl get nodes"]
    r3["You deploy apps<br/>and learn Kubernetes"]
    p1 --> r1 --> p2 --> r2 --> p3 --> r3
```

- **Phase 1** is covered in [Create the VMs](../tutorials/02-create-vms.md).
- **Phase 2** is [Prepare the nodes](../tutorials/03-prepare-nodes.md), then [Install the cluster](../tutorials/04-install-the-cluster.md).
- **Phase 3** is where the learning happens.

# What happens in phase 2, step by step

```mermaid
sequenceDiagram
    participant You as Your computer (Ansible)
    participant CP as control-plane
    participant N as node-1 and node-2

    You->>CP: SSH: prepare.yml (OS upgrade, /data on LVM, swap off, kernel settings)
    You->>N: SSH: prepare.yml (OS upgrade, /data on LVM, swap off, kernel settings)
    You->>CP: SSH: install the Kubernetes control plane
    CP-->>You: kubeconfig file
    You->>CP: SSH: get a join token (kubeadm: a new 10 minute token per node)
    CP-->>You: join token
    You->>N: SSH: join the node with the token
    N->>CP: join the cluster
    You->>CP: SSH: kubeadm only, delete the token again
    You->>CP: SSH: Helm installs Cilium (pod network, no kube-proxy)
    Note over You: kubectl get nodes<br/>control-plane, node-1, node-2 are Ready
```

- The control plane is always installed first, because `node-1` and `node-2` need it to join.
- The **join token** is a password that proves a node is allowed to join. With kubeadm, kube-lab makes a new one for each node and deletes it right after the join: [Joining nodes explained](joining-nodes-explained.md).
- The **kubeconfig** file holds the address and credentials kubectl needs.

# Why Vagrant and Ansible are kept apart

- Vagrant only makes VMs. It has no install scripts in it.
- Ansible only installs software. It does not care where the VMs came from.
- Benefits:
  - You can wipe Kubernetes and reinstall it without rebuilding the VMs. That is much faster.
  - You can point the same Ansible files at real servers or cloud VMs later.
  - Each tool is easier to learn on its own.
- The decision is recorded in [ADR 0004](../../adr/0004-keep-vms-and-provisioning-separate.md).

# The life of the lab

```mermaid
stateDiagram-v2
    [*] --> EmptyVMs: vagrant up
    EmptyVMs --> Kubeadm: site.yml with k8s_distro kubeadm
    EmptyVMs --> RKE2: site.yml with k8s_distro rke2
    Kubeadm --> Kubeadm: raise a version, site.yml again
    RKE2 --> RKE2: raise a version, site.yml again
    Kubeadm --> EmptyVMs: reset.yml
    RKE2 --> EmptyVMs: reset.yml
    EmptyVMs --> [*]: vagrant destroy -f

    EmptyVMs: Prepared VMs, no Kubernetes
    Kubeadm: kubeadm cluster
    RKE2: RKE2 cluster
```

- **Build**: `vagrant up` once, set `k8s_distro` in `cluster.yml`, run `ansible-playbook site.yml`.
- **Upgrade**: raise a version in `cluster.yml`, run `site.yml` again.
- **Switch**: run `reset.yml`, change `k8s_distro`, run `site.yml`. The VMs stay.
- **Pause**: `vagrant halt` shuts the VMs down. Everything is kept for next time.
- **Remove**: `vagrant destroy -f` deletes all 3 VMs and their disks, from any state.

# Where everything lives

- See [Repo layout](../reference/repo-layout.md) for every file and folder in the repo.
