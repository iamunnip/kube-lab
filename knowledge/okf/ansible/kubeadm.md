---
type: Ansible Role
title: kubeadm
description: Installs or upgrades plain Kubernetes with kubeadm. containerd with data on /data, packages held at kubernetes_version, kubeadm init and join from YAML config, a short lived join token per node, no kube-proxy, kubeadm upgrade, distro marker. Also removes kubeadm for reset.yml.
tags: [ansible, role, kubernetes, kubeadm, containerd]
resource: ../../../ansible/roles/kubeadm
status: stable
sources:
  - id: adr-0009
    resource: ../../../docs/adr/0009-kubeadm-with-containerd-and-cilium.md
    title: "ADR 0009: kubeadm with containerd, Cilium with Helm"
  - id: adr-0012
    resource: ../../../docs/adr/0012-cilium-replaces-kube-proxy.md
    title: "ADR 0012: Cilium replaces kube-proxy"
  - id: adr-0013
    resource: ../../../docs/adr/0013-short-lived-join-tokens.md
    title: "ADR 0013: Short lived join tokens, made per node by Ansible"
  - id: adr-0014
    resource: ../../../docs/adr/0014-config-files-in-the-roles.md
    title: "ADR 0014: Config files live in the roles, vars hold only values"
---

# Facts

| Field                | Value                                                         |
|----------------------|---------------------------------------------------------------|
| Folder               | `ansible/roles/kubeadm/`                                      |
| Run by               | `site.yml` when `k8s_distro` is `kubeadm`, after `prepare.yml`[^adr-0009] |
| Settings             | `group_vars/all/kubeadm.yml`, version in `cluster.yml`        |
| Upgrade              | When `kubernetes_version` is higher than installed: upgrade apply, upgrade node, new kubelet |
| Reset tasks          | `tasks/reset.yml`, used by `reset.yml`                        |
| Kubernetes           | `kubernetes_version`, packages from `pkgs.k8s.io`, held       |
| Container runtime    | containerd 2.2.2 (Ubuntu), `root = /data/containerd`, `SystemdCgroup = true` |
| kubeadm config API   | `kubeadm.k8s.io/v1beta4`, checked with `kubeadm config validate` |
| Config templates     | `templates/kubeadm-init.yaml.j2` (Init, Cluster, Kubelet configuration) and `templates/kubeadm-join.yaml.j2` (JoinConfiguration). Vars hold only values[^adr-0014] |
| Init config          | `/etc/kube-lab/kubeadm-init.yaml` (0600) on the control plane |
| Join config          | `/etc/kube-lab/kubeadm-join.yaml` (0600), only during the join, then deleted |
| Init token           | `bootstrapTokens` with no token value, `ttl: 15m` (`kubeadm_init_token_ttl`), never used[^adr-0013] |
| Join token           | `kubeadm token create --ttl 10m` per node on the control plane, deleted in an `always:` block[^adr-0013] |
| CA hash              | sha256 of the public key in `/etc/kubernetes/pki/ca.crt`, worked out with `openssl` |
| Secrets in the log   | Token tasks `no_log: true`, join template `diff: false`, init output without token lines |
| kube-proxy           | Not installed: `kubeadm_kube_proxy_disabled: true`, used as `proxy.disabled`[^adr-0012] |
| kubelet              | `node-ip` = private IP, `root-dir` = `/data/kubelet`, `cgroupDriver: systemd` |
| etcd data            | `/data/etcd`                                                  |
| Distro marker        | `/etc/kube-lab/distro` = `kubeadm`                            |
| Tags                 | `containerd`, `packages`, `init`, `join`, `upgrade`, `marker` |
| Tested               | 2026-11-08: 3 nodes Ready, second run `changed=0`. Again on fresh VMs with the role templates: init and join from `--config`, each join token deleted, `verify-network.yml` `failed=0`, rerun `changed=0`. Join token steps tested on the live cluster: token made with a 10 minute TTL, CA hash matches kubeadm's, 0 kube-lab tokens left |

# Related

- Needs [prereq](prereq.md) and [k8s_prepare](k8s-prepare.md) first.
- Followed by [cilium](cilium.md), the pod network.
- Builds the [kubeadm](../distributions/kubeadm.md) distribution.

# Learn more

- [Installing Kubernetes explained](../../../docs/diataxis/explanation/installing-kubernetes-explained.md)
- [Tutorial 4](../../../docs/diataxis/tutorials/04-install-the-cluster.md)
- [Joining nodes explained](../../../docs/diataxis/explanation/joining-nodes-explained.md)
- [Rejoin a node](../../../docs/diataxis/how-to/rejoin-a-node.md)

[^adr-0009]: ADR 0009: kubeadm with containerd, Cilium with Helm
[^adr-0012]: ADR 0012: Cilium replaces kube-proxy
[^adr-0013]: ADR 0013: Short lived join tokens, made per node by Ansible
[^adr-0014]: ADR 0014: Config files live in the roles, vars hold only values
