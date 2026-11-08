---
type: Disk
title: OS disk
description: Disk 1 in every node. The box's own 10 GB disk, not resized. Ubuntu, Kubernetes and container images live here.
tags: [disk, storage, os]
resource: ../../../vagrant/single-control-plane/Vagrantfile
status: stable
sources:
  - id: adr-0006
    resource: ../../../docs/adr/0006-two-disks-per-node.md
    title: "ADR 0006: Two disks per node"
  - id: box-ovf
    resource: ~/.vagrant.d/boxes/cloud-image-VAGRANTSLASH-ubuntu-26.04/20260927.0.0/amd64/virtualbox/box.ovf
    title: Box disk capacity, 10737418240 bytes
---

# Facts

| Field              | Value                                                      |
|--------------------|------------------------------------------------------------|
| Size               | 10 GB, from the box[^box-ovf]                              |
| Resized            | No. There is no OS disk line in the `Vagrantfile`[^adr-0006] |
| File               | `box.vmdk`, copied from the box into the VM's folder       |
| Space used when new| About 2.2 GB (measured)                                    |
| Allocation         | Dynamic. Grows up to 10 GB                                 |
| Device             | `/dev/sda` (seen on all 3 VMs)                             |
| Partitions         | `sda1` 8.6 GB `/`, `sda13` 1 GB `/boot`, `sda14` 4 MB BIOS boot, `sda15` 106 MB `/boot/efi` |
| Free on `/` when new | About 6.7 GB (measured)                                  |
| Controller         | VirtIO SCSI, from the box                                  |
| Deleted by         | `vagrant destroy`                                          |

# Rules

- Can grow, never shrink. VirtualBox refuses to make a disk smaller.
- Container images go to the [data disk](data-disk.md), so `/` only holds the OS. Check it now and then with `df -h /`.

# Learn more

- [Disks and storage](../../../docs/diataxis/explanation/disks-and-storage.md)

[^adr-0006]: ADR 0006: Two disks per node
[^box-ovf]: Box disk capacity, 10737418240 bytes
