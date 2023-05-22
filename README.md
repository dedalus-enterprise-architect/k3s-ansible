# Build a Kubernetes cluster using k3s via Ansible

Author: <https://github.com/itwars>

## K3s Ansible Playbook

Build a Kubernetes cluster using Ansible with k3s. The goal is easily install a Kubernetes cluster on machines running:

- [X] Debian
- [X] Ubuntu
- [X] CentOS

on processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## System requirements

Deployment environment must have Ansible 2.4.0+
Master and nodes must have passwordless SSH access

## Variables

|Variable|Example|Description|
|--|--|--|
|ansible_user|ubuntu|user to connect via ssh to to master|
|ansible_ssh_private_key_file|./secrets/id_rsa|ssh private key file|
|k3s_version|1.25.9+k3s1|k3s version from https://github.com/k3s-io/k3s/releases|
|systemd_dir|/etc/systemd/system|path to systemd|

## Single node cluster

Modify the inventory file in `inventory/single_node/hosts.ini` adding the IP/FQDN of the single node in the `master` section.
Also edit `inventory/single_node/group_vars/all.yml` to match your environment.

```bash
ansible-playbook site.yml -i inventory/single_node/hosts.ini"
```

## Multiple nodes cluster

First create a new directory based on the `sample` directory within the `inventory` directory:

```bash
cp -R inventory/sample inventory/my-cluster
```

Second, edit `inventory/my-cluster/hosts.ini` to match the system information gathered above. For example:

```bash
[master]
192.16.35.12

[node]
192.16.35.[10:11]

[k3s_cluster:children]
master
node
```

If needed, you can also edit `inventory/my-cluster/group_vars/all.yml` to match your environment.

Start provisioning of the cluster using the following command:

```bash
ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
```

## Reset node

Run the following playbook against the original inventory used to deploy the nodes, ex.

```bash
ansible-playbook reset.yml -i inventory/single_node/hosts.ini
```

## Kubeconfig

To get access to your **Kubernetes** cluster just

```bash
scp ubuntu@master_ip:~/.kube/config ~/.kube/config
```
