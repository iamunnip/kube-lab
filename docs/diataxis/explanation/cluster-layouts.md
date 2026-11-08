# Cluster layouts

- This page explains why each cluster layout has its own folder and `Vagrantfile`, what the two layouts are, and how kube-vip fits into the planned HA layout.

# The short version

- A **layout** is one shape of cluster: how many control planes, how many nodes, which IPs.
- Each layout lives in its own folder under `vagrant/`, with its own `Vagrantfile`.
- You `cd` into a layout's folder before running any `vagrant` command.

```mermaid
flowchart LR
    n0["<b>vagrant/</b>"]
    n1["<b>README.md</b><br/>What each layout is"]
    n2["<b>single-control-plane/</b><br/>Ready: 1 control plane, 2 nodes"]
    n3["<b>Vagrantfile</b>"]
    n4["<b>ha-control-plane/</b><br/>Planned: 3 control planes, 1 node, kube-vip"]
    n5["<b>Vagrantfile</b>"]
    n0 --> n1
    n0 --> n2
    n2 --> n3
    n0 --> n4
    n4 --> n5
```

# Why one folder per layout

- Vagrant always reads the file named `Vagrantfile` in the folder you are in.
- Vagrant keeps the state of the VMs (their IDs and SSH keys) in a `.vagrant/` folder next to that `Vagrantfile`.
- One folder per layout means:
  - Each layout has its own `Vagrantfile`, with no `if` statements to switch between layouts.
  - Each layout has its own `.vagrant/` state, so `vagrant destroy` in one folder never touches the other.
  - `vagrant status` in a folder only shows that layout's VMs.

```mermaid
flowchart LR
    you["You run vagrant up"]
    q{"Which folder<br/>are you in?"}
    s["vagrant/single-control-plane/<br/>reads its Vagrantfile<br/>state in its own .vagrant/"]
    h["vagrant/ha-control-plane/<br/>reads its Vagrantfile<br/>state in its own .vagrant/"]
    you --> q
    q -- single-control-plane --> s
    q -- ha-control-plane --> h
```

- The decision is recorded in [ADR 0007](../../adr/0007-one-folder-per-cluster-layout.md).

# Layout 1: single-control-plane (ready)

```mermaid
flowchart TB
    cp["control-plane<br/>192.168.56.10"]
    n1["node-1<br/>192.168.56.11"]
    n2["node-2<br/>192.168.56.12"]
    n1 -- "API on 6443" --> cp
    n2 -- "API on 6443" --> cp
```

- 1 control plane and 2 nodes.
- Simple to understand. Every tutorial so far uses it.
- **Weak point**: if `control-plane` goes down, the cluster cannot be managed until it comes back. Running apps keep running, but nothing new can be scheduled.

# Layout 2: ha-control-plane (planned)

- HA means **high availability**: the cluster keeps working when one control plane fails.
- 3 control planes and 1 node.
- A **virtual IP** (VIP) from **kube-vip** sits in front of the 3 control planes.

```mermaid
flowchart TB
    kubectl["kubectl and node-1<br/>always talk to the VIP"]
    vip(["Virtual IP 192.168.56.20:6443<br/>held by one control plane at a time"])
    cp1["control-plane-1<br/>192.168.56.21<br/>kube-vip leader"]
    cp2["control-plane-2<br/>192.168.56.22"]
    cp3["control-plane-3<br/>192.168.56.23"]
    n1["node-1<br/>192.168.56.24"]
    kubectl --> vip
    n1 --> vip
    vip --> cp1
    cp1 <-.->|etcd| cp2
    cp2 <-.->|etcd| cp3
    cp3 <-.->|etcd| cp1
```

# Why 3 control planes

- The control plane stores everything in **etcd**.
- etcd only works while a **majority** of its members are up. This is called a **quorum**.

| Control planes | Majority needed | Can lose | Worth it?                          |
|----------------|-----------------|----------|------------------------------------|
| 1              | 1               | 0        | No HA at all                       |
| 2              | 2               | 0        | No better than 1, and more to break |
| 3              | 2               | 1        | Yes. The smallest real HA setup    |
| 5              | 3               | 2        | For large clusters                 |

- That is why the HA layout has **3**, not 2.

# What kube-vip does

- With 3 control planes, there are 3 API server addresses. kubectl and the nodes need **one** address that always works.
- kube-vip runs on each control plane and provides that one address: the **virtual IP**.

```mermaid
sequenceDiagram
    participant C1 as control-plane-1
    participant C2 as control-plane-2
    participant C3 as control-plane-3
    participant N as kubectl and node-1

    Note over C1,C3: kube-vip on all 3 elects a leader
    C1->>C1: becomes leader, takes the VIP 192.168.56.20
    N->>C1: requests to 192.168.56.20:6443
    Note over C1: control-plane-1 goes down
    C2->>C2: elected new leader, takes the VIP
    N->>C2: same address 192.168.56.20:6443 keeps working
```

- Only the **leader** answers for the VIP on the network.
- If the leader fails, another control plane becomes leader and takes over the VIP within seconds.
- kubectl and `node-1` never change the address they use.
- The VIP is not tied to any one VM, so it is not in any VM's network card settings. kube-vip adds it.

# How the two layouts compare

| Topic                       | single-control-plane            | ha-control-plane (planned)              |
|-----------------------------|---------------------------------|-----------------------------------------|
| Folder                      | `vagrant/single-control-plane/` | `vagrant/ha-control-plane/`             |
| Control planes              | 1: `control-plane`              | 3: `control-plane-1` to `control-plane-3` |
| Nodes                       | 2: `node-1`, `node-2`           | 1: `node-1`                             |
| API address                 | `192.168.56.10:6443`            | VIP `192.168.56.20:6443`                |
| IP range                    | `192.168.56.10` to `.12`        | `192.168.56.20` to `.24`                |
| VirtualBox name prefix      | `kube-lab-single-`              | `kube-lab-ha-`                          |
| Survives a control plane failure | No                         | Yes, 1 of 3                             |
| Total CPU and memory        | 9 CPUs, 18 GB                   | 12 CPUs, 24 GB                          |
| Best for                    | Learning the basics             | Learning HA, upgrades, failure tests    |

# Running both layouts

- They use different VM names and different IPs, so both can **exist** at the same time.
- Running both **at once** needs 21 CPUs and 22 GB of memory. On a 12 thread computer, run one at a time:

**Run on: your computer**

```bash
cd kube-lab/vagrant/single-control-plane
vagrant halt
cd ../ha-control-plane
vagrant up
```

- The commands for the HA layout will work once its `Vagrantfile` is added.
