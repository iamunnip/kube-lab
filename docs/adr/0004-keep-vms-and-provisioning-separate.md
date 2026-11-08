# 0004: Vagrant only makes VMs, Ansible installs everything

- **Status**: accepted
- **Date**: 2026-11-08

# Context

- Vagrant can run install scripts inside VMs (called provisioners).
- The lab needs to install 3 different Kubernetes distributions on the same VMs, one at a time.
- The setup should also work on real servers later.

# Decision

- The `Vagrantfile` has **no provisioners and no scripts**. It only creates VMs.
- **Ansible**, run from your computer, does all installing and configuring.

```mermaid
flowchart LR
    subgraph vagrant["Vagrant: makes VMs"]
        v1["sets CPU and memory"]
        v2["sets hostname and IP"]
        v3["adds the 2 disks"]
        v4["nothing else"]
    end
    subgraph ansible["Ansible: installs everything"]
        a1["sets kernel options"]
        a2["installs Kubernetes"]
        a3["joins nodes to the cluster"]
        a4["resets and switches distributions"]
    end
    vagrant -- "then" --> ansible
```

# Why

- Switching from kubeadm to RKE2 only needs Ansible. The VMs do not have to be rebuilt.
- The same Ansible files work on real servers or cloud VMs, which Vagrant scripts would not.
- Each tool is easier to learn when it has one job.
- No shell scripts to read or debug.

# Options we looked at

| Option                          | Good                             | Bad                                                 |
|---------------------------------|----------------------------------|-----------------------------------------------------|
| Vagrant makes VMs, Ansible installs | Clear split, reusable        | Two commands to build the full lab                  |
| Shell scripts in the Vagrantfile| One command does everything      | Hard to reuse, hard to switch distributions         |
| Vagrant's built-in Ansible provisioner | One command                | Ties Ansible to Vagrant, harder to run on real servers |

# Consequences

- **Good**: Kubernetes can be wiped and reinstalled in minutes.
- **Bad**: beginners run two steps, `vagrant up` then `ansible-playbook`.
- **Follow up**: Ansible connects to the private IPs using the SSH keys Vagrant makes in `vagrant/<layout>/.vagrant/`.
