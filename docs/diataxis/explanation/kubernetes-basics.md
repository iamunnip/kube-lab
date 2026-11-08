# Kubernetes basics

- This page explains what Kubernetes is and what runs on each VM in the lab.
- There are no commands to run here. It is background for the Kubernetes tutorials.

# What problem Kubernetes solves

- Apps today are often packaged as **containers**. A container holds an app and everything it needs to run.
- Running one container on one computer is easy. Running many containers across many computers is hard:
  - Which computer should each container run on?
  - What happens when a container crashes?
  - What happens when a whole computer goes down?
  - How do containers on different computers find each other?
- **Kubernetes** answers all of these. You tell it what you want, and it keeps making it true.

# The main idea: desired state

```mermaid
flowchart LR
    you["You:<br/>I want 3 copies of my web app"]
    k8s["Kubernetes:<br/>stores what you want,<br/>compares it with what is running"]
    nodes["node-1 and node-2:<br/>run the 3 copies"]
    you --> k8s
    k8s -- "start or stop copies" --> nodes
    nodes -- "report: one copy crashed" --> k8s
```

- You write down the **desired state**: what should be running.
- Kubernetes keeps checking the **actual state**: what is really running.
- When they differ, Kubernetes acts to fix it. If a copy crashes, it starts a new one. This never stops.

# The parts of a cluster

- A **cluster** is a group of computers, called **nodes**, managed by Kubernetes as one.
- In this lab the cluster has 3 nodes:

```mermaid
flowchart TB
    kubectl["kubectl<br/>on your computer"]

    subgraph cp["control-plane 192.168.56.10"]
        api["kube-apiserver<br/>the front door"]
        etcd[("etcd<br/>the database")]
        sched["kube-scheduler<br/>picks a node"]
        ctrl["kube-controller-manager<br/>fixes differences"]
    end

    subgraph n1["node-1 192.168.56.11"]
        k1["kubelet"]
        p1["Cilium eBPF Service routing"]
        c1["containerd"]
        pods1["pods"]
    end

    subgraph n2["node-2 192.168.56.12"]
        k2["kubelet"]
        p2["Cilium eBPF Service routing"]
        c2["containerd"]
        pods2["pods"]
    end

    kubectl --> api
    api <--> etcd
    sched <--> api
    ctrl <--> api
    k1 <--> api
    k2 <--> api
    k1 --> c1 --> pods1
    k2 --> c2 --> pods2
```

# What runs on the control plane

- The **control plane** is the brain. It decides; it does not usually run your apps.

| Part                       | Its job                                                         |
|----------------------------|-----------------------------------------------------------------|
| `kube-apiserver`           | The front door. Every request, from you or from other parts, goes through it. |
| `etcd`                     | The database. Stores the desired state and the current state of everything. |
| `kube-scheduler`           | Picks which node each new pod should run on.                    |
| `kube-controller-manager`  | Runs the loops that compare desired and actual state and fix differences. |

- The control plane also runs `kubelet` and a container runtime, because its own parts run as containers too.

# What runs on node-1 and node-2

- These nodes run your apps.

| Part          | Its job                                                              |
|---------------|----------------------------------------------------------------------|
| `kubelet`     | The node's agent. Asks the API server what should run here, then makes it happen. |
| `containerd`  | The container runtime. Actually starts and stops containers.        |
| Service routing | Sends Service traffic to the right pods. This lab uses Cilium eBPF instead of kube-proxy. |
| CNI plugin    | Gives every pod an IP address and connects pods across nodes. Examples: Flannel, Calico, Cilium. |

# What happens when you deploy an app

```mermaid
sequenceDiagram
    participant You as You (kubectl)
    participant API as kube-apiserver
    participant DB as etcd
    participant CM as controller-manager
    participant S as scheduler
    participant K as kubelet on node-1 and node-2
    participant C as containerd

    You->>API: kubectl apply -f web.yaml (3 copies)
    API->>DB: save the desired state
    CM->>API: sees 3 wanted, 0 running
    CM->>API: create 3 pod records
    S->>API: assign pods to node-1, node-2, node-1
    K->>API: which pods are mine?
    K->>C: download the image, start the containers
    K->>API: report: running
```

# The objects you will use first

| Object       | What it is                                                         | Example                            |
|--------------|--------------------------------------------------------------------|------------------------------------|
| Node         | One computer in the cluster.                                       | `control-plane`, `node-1`, `node-2`|
| Pod          | The smallest thing Kubernetes runs: one or more containers together. It gets its own IP. | One copy of a web server |
| Deployment   | Keeps a set number of identical pods running, and updates them safely. | "Always run 3 web pods"        |
| Service      | A stable name and IP in front of a group of pods. Pods come and go; the service stays. | `web` service on port 80 |
| Namespace    | A folder for objects, to keep things apart.                        | `default`, `kube-system`           |

```mermaid
flowchart LR
    svc["Service: web<br/>stable IP"]
    dep["Deployment: web<br/>wants 3 pods"]
    pa["Pod web-1<br/>on node-1"]
    pb["Pod web-2<br/>on node-2"]
    pc["Pod web-3<br/>on node-1"]
    dep -- creates --> pa
    dep -- creates --> pb
    dep -- creates --> pc
    svc -- sends traffic to --> pa
    svc -- sends traffic to --> pb
    svc -- sends traffic to --> pc
```

# How you talk to the cluster

- **kubectl** is the command line tool for Kubernetes.
- It reads a file called **kubeconfig** to know:
  - Where the API server is: `https://192.168.56.10:6443`.
  - Which credentials to use.
- In this lab, Ansible will copy the kubeconfig to your computer so you can run `kubectl` there.

# How this maps to kubeadm and RKE2

- Both give you the same parts listed above. They differ in how the parts are packaged and installed.
- In RKE2, the control plane is called a **server** and the other nodes are called **agents**.
- In kubeadm, you run `kubeadm init` on the control plane and `kubeadm join` on the other nodes.
- Details: [kubeadm vs RKE2](kubeadm-vs-rke2.md).
