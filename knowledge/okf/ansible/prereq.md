---
type: Ansible Role
title: prereq
description: OS level basics for every VM. Switches off 2 GRUB services that fail on every boot, upgrades the OS, and makes the data disk an LVM volume mounted at /data.
tags: [ansible, role, os, lvm, disk]
resource: ../../../ansible/roles/prereq
status: stable
sources:
  - id: adr-0008
    resource: ../../../docs/adr/0008-prereq-and-k8s-prepare-roles.md
    title: "ADR 0008: Two Ansible roles before Kubernetes"
---

# Facts

| Field                | Value                                                         |
|----------------------|---------------------------------------------------------------|
| Folder               | `ansible/roles/prereq/`                                       |
| Run by               | `ansible/prepare.yml`, first, on group `cluster`[^adr-0008]   |
| Runs as              | root (`become: true`)                                         |
| Tested               | 2026-11-08 on all 3 VMs: first run `failed=0`, second run `changed=0` |
| Tags                 | `grub`, `os_upgrade`, `data_disk`                             |

# Steps

| Tag          | What it does                                                                  |
|--------------|-------------------------------------------------------------------------------|
| `grub`       | Masks `grub2-common.service` and `grub-initrd-fallback.service`, then `systemctl reset-failed` |
| `os_upgrade` | `apt update`, `apt dist-upgrade`, `autoremove`, reboot if `/var/run/reboot-required` exists |
| `data_disk`  | `/dev/sdb` to PV, VG `data_vg`, LV `data_lv` (100% free), ext4, mount at `/data`, `/etc/fstab` line `/dev/data_vg/data_lv /data ext4 defaults,nofail 0 0` |

# Safety

- Skips the disk if `data_vg` already exists.
- Stops if `wipefs --no-act /dev/sdb` finds anything on the disk.
- Never formats a volume that already has a filesystem.

# Related

- Makes the [data disk](../storage/data-disk.md) ready. Runs before [k8s_prepare](k8s-prepare.md).

# Learn more

- [The Ansible roles explained](../../../docs/diataxis/explanation/ansible-roles-explained.md)
- [Ansible settings](../../../docs/diataxis/reference/ansible-settings.md)

[^adr-0008]: ADR 0008: Two Ansible roles before Kubernetes
