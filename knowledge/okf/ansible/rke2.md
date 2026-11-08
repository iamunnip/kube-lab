---
type: Ansible Role
title: rke2
description: Installs or upgrades RKE2. Server on the control plane, agents on the nodes, config files in templates/ (config-server.yaml.j2, config-agent.yaml.j2) with values from rke2.yml, distro marker. Also removes RKE2 for reset.yml.
tags: [ansible, role, kubernetes, rke2]
resource: ../../../ansible/roles/rke2
status: stable
sources:
  - id: adr-0010
    resource: ../../../docs/adr/0010-one-site-playbook-driven-by-vars.md
    title: "ADR 0010: One site playbook, every setting in vars files"
---

# Facts

| Field                | Value                                                         |
|----------------------|---------------------------------------------------------------|
| Folder               | `ansible/roles/rke2/`                                         |
| Run by               | `site.yml` when `k8s_distro` is `rke2`[^adr-0010]             |
| Settings             | `group_vars/all/rke2.yml`, version in `cluster.yml`           |
| Install              | Only the tarball `rke2.linux-amd64.tar.gz` from the GitHub release, unpacked into `/usr/local`. No install script, no checksum file |
| Upgrade              | Runs when `rke2_version` differs from `rke2 --version`. Stops on a downgrade |
| Reset tasks          | `tasks/reset.yml`, used by `reset.yml`                        |
| Config files         | `templates/config-server.yaml.j2`, `templates/config-agent.yaml.j2`, values from `rke2.yml` |
| CPU type             | `rke2_arch: amd64` only                                       |
| Tags                 | `install`, `server`, `agent`, `marker`                        |
| Tested               | 2026-11-08 on fresh VMs: tarball install of v1.37.2+rke2r1, 3 nodes Ready, no kube-proxy pods, Cilium `KubeProxyReplacement: True`, `verify-network.yml` `failed=0`, two more `site.yml` runs `changed=0` |

# Learn more

- [Installing Kubernetes explained](../../../docs/diataxis/explanation/installing-kubernetes-explained.md)

[^adr-0010]: ADR 0010: One site playbook, every setting in vars files
