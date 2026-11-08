# How to update the VMs

- Use this page to install the latest Ubuntu updates on all VMs.
- It runs only the OS upgrade part of the `prereq` role.

# Update all VMs

**Run on: your computer** (in `ansible/`)

```bash
cd kube-lab/ansible
ansible-playbook prepare.yml --tags os_upgrade
```

- `--tags os_upgrade` runs only the tasks tagged `os_upgrade`: `apt update`, `apt dist-upgrade`, and a reboot if Ubuntu asks for one.
- Each VM that needs a reboot is rebooted. Ansible waits for it to come back.

**Expected result**

- `failed=0` for every VM in the `PLAY RECAP`.
- `changed` is `0` if there were no updates.

# Update one VM only

```bash
ansible-playbook prepare.yml --tags os_upgrade --limit node-1
```

- `--limit node-1` runs on `node-1` only. Use the names from `inventories/single-control-plane/hosts.ini`.

# Update without rebooting

```bash
ansible-playbook prepare.yml --tags os_upgrade -e os_reboot_if_needed=false
```

- `-e` sets a setting for this run only.
- Reboot later yourself with `vagrant reload <name>` from `vagrant/single-control-plane/`.

# Check which VMs need a reboot

```bash
ansible cluster -m ansible.builtin.stat -a "path=/var/run/reboot-required"
```

- `exists: true` means that VM wants a reboot.
