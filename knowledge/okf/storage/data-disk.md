---
type: Disk
title: Data disk
description: Disk 2 in every node. 20 GB, created empty by Vagrant, then made an LVM volume mounted at /data by the prereq Ansible role. All Kubernetes data goes here.
tags: [disk, storage, data, lvm]
resource: ../../../vagrant/single-control-plane/Vagrantfile
status: stable
sources:
  - id: adr-0008
    resource: ../../../docs/adr/0008-prereq-and-k8s-prepare-roles.md
    title: "ADR 0008: Two Ansible roles before Kubernetes"
  - id: adr-0006
    resource: ../../../docs/adr/0006-two-disks-per-node.md
    title: "ADR 0006: Two disks per node"
  - id: vagrant-disks
    resource: https://developer.hashicorp.com/vagrant/docs/disks/usage
    title: Vagrant disks usage
---

# Facts

| Field              | Value                                                      |
|--------------------|------------------------------------------------------------|
| Size               | 20 GB (`DATA_DISK_SIZE`)[^adr-0006]                        |
| Vagrantfile line   | `vm.disk :disk, name: "<node>-data", size: DATA_DISK_SIZE` |
| File               | `<node>-data.vdi` in `<VirtualBox machine folder>/kube-lab-single-<node>/`[^vagrant-disks] |
| Device             | `/dev/sdb` (seen on all 3 VMs)                             |
| LVM                | PV `/dev/sdb`, VG `data_vg`, LV `data_lv` using all space[^adr-0008] |
| Filesystem         | ext4, about 19.5 GB usable                                 |
| Mounted at         | `/data`                                                    |
| fstab line         | `/dev/data_vg/data_lv /data ext4 defaults,nofail 0 0`      |
| Set up by          | The [prereq](../ansible/prereq.md) Ansible role            |
| Folders            | `/data/containerd`, `/data/rancher`, `/data/kubelet`, `/data/storage`, made by [k8s_prepare](../ansible/k8s-prepare.md) |
| Deleted by         | `vagrant destroy`, or removing its line and running `vagrant reload` |

# Used for

- All Kubernetes data: container images, pod data and app volumes, so the [OS disk](os-disk.md) never fills up.

# Learn more

- [Disks and storage](../../../docs/diataxis/explanation/disks-and-storage.md)
- [The Ansible roles explained](../../../docs/diataxis/explanation/ansible-roles-explained.md)

[^adr-0006]: ADR 0006: Two disks per node
[^adr-0008]: ADR 0008: Two Ansible roles before Kubernetes
[^vagrant-disks]: Vagrant disks usage
