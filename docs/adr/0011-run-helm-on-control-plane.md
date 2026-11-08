# 0011: Run Helm and manage Cilium on the control plane

- **Status**: accepted
- **Date**: 2026-11-08

# Decision

Ansible connects over SSH to the first control plane, installs the pinned Helm binary in `/data/helm/bin/helm`, and installs or upgrades Cilium there. It uses the `vagrant` user's kubeconfig and keeps Helm files in `/data/helm`. No plugins and no checksum or signature checks (changed 2026-11-08: the helm-diff plugin and its key downloads from GitHub kept failing).

# Consequences

The controller no longer needs Helm to provision Cilium. The control plane needs internet access to download Helm, the plugin and charts. Local kubeconfig export and reset cleanup still run on the controller so users can run kubectl locally. This supersedes ADR 0009's choice of a local Helm directory.
