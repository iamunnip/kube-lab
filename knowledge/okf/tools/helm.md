---
type: Tool
title: Helm
description: The package manager for Kubernetes. Runs on the control plane and installs Cilium for the cilium Ansible role.
tags: [tool, helm, host, kubernetes]
resource: https://helm.sh/
status: stable
---

# Facts

| Field                  | Value                                               |
|------------------------|-----------------------------------------------------|
| Runs on                | First control plane over SSH                                       |
| Installed with         | Ansible: pinned release from get.helm.sh, in `/data/helm/bin`   |
| Pinned control-plane version | v4.3.1 (`helm_version` in `cilium.yml`), no plugins |
| Used by                | The [cilium](../ansible/cilium.md) Ansible role, through `kubernetes.core.helm` |
| Lab's own setup        | `/data/helm/` (repository list and cache), so your `~/.config/helm` is never used by the lab |
| Values file            | `/data/helm/cilium-values.yaml`, from the cilium role's `templates/values.yaml.j2` |

# Related tools on your computer

| Tool        | Version  | Installed with             | Used for                         |
|-------------|----------|----------------------------|----------------------------------|
| helm        | v4       | Homebrew (optional)        | Looking at releases by hand: `helm list -A`. The lab does not need it |
| kubectl     | v1.37.2  | Homebrew                   | Talking to the cluster           |
| cilium-cli  | v0.20.2  | Homebrew: `brew install cilium-cli` | `cilium status`         |
