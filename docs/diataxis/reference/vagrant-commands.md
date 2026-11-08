# Vagrant commands

- Run every command **on your computer, in the layout folder** `vagrant/single-control-plane/`.
- Add a VM name (`control-plane`, `node-1`, `node-2`) to act on one VM. Leave it out to act on all 3.
- The **Data** column tells you if a command can delete your work.

# Check

| Command                  | What it does                                          | Data    |
|--------------------------|-------------------------------------------------------|---------|
| `vagrant validate`       | Checks the `Vagrantfile` for mistakes.                | Safe    |
| `vagrant status`         | Shows the state of each VM.                           | Safe    |
| `vagrant global-status`  | Shows every Vagrant VM on your computer, from any folder. | Safe |
| `vagrant ssh-config`     | Prints the SSH address, port, user and key for each VM. | Safe  |

# Start and stop

| Command                  | What it does                                          | Data    |
|--------------------------|-------------------------------------------------------|---------|
| `vagrant up`             | Creates VMs that do not exist, starts VMs that are off. | Safe  |
| `vagrant halt`           | Shuts VMs down cleanly.                               | Safe    |
| `vagrant suspend`        | Pauses VMs and saves their memory to disk.            | Safe    |
| `vagrant resume`         | Continues paused VMs.                                 | Safe    |
| `vagrant reload`         | Restarts VMs and applies `Vagrantfile` changes (CPU, memory, network). | Safe |

# Use

| Command                         | What it does                                   | Data    |
|---------------------------------|------------------------------------------------|---------|
| `vagrant ssh control-plane`     | Opens a terminal inside the VM. Type `exit` to leave. | Safe |
| `vagrant ssh node-1 -c "hostname"` | Runs one command inside the VM and returns. | Safe   |

# Delete

| Command                    | What it does                                        | Data          |
|----------------------------|-----------------------------------------------------|---------------|
| `vagrant destroy node-1`   | Deletes the VM and its disk. Asks first.            | **Deletes VM**|
| `vagrant destroy -f`       | Deletes all VMs and disks. Does not ask.            | **Deletes VMs**|

# Boxes (OS images)

| Command                               | What it does                                    | Data    |
|---------------------------------------|-------------------------------------------------|---------|
| `vagrant box list`                    | Lists downloaded boxes and versions.            | Safe    |
| `vagrant box outdated`                | Checks if a newer box version exists.           | Safe    |
| `vagrant box update`                  | Downloads the newer version. Existing VMs keep the old one until rebuilt. | Safe |
| `vagrant box prune`                   | Deletes old box versions no VM uses.            | Deletes old images |
| `vagrant box remove cloud-image/ubuntu-26.04` | Deletes the box. Next `vagrant up` downloads it again. | Deletes image |

# Where Vagrant keeps things

| Path                          | What is in it                                     | In git?  |
|-------------------------------|---------------------------------------------------|----------|
| `vagrant/single-control-plane/Vagrantfile` | The VM definitions for this layout   | Yes      |
| `vagrant/single-control-plane/.vagrant/`   | Each VM's ID and SSH key, made by `vagrant up` | No, in `.gitignore` |
| `~/.vagrant.d/boxes/`         | Downloaded boxes, shared by all your projects     | No       |
| `<VirtualBox machine folder>/kube-lab-single-<node>/` | Each VM's settings and its 2 disk files. Deleted by `vagrant destroy` | No |
