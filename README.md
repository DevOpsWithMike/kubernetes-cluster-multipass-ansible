# Kubernetes cluster deployment
Create a kubernetes cluster from scratch with [multipass](https://multipass.run/install) virtual machines and [ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).



To create the vms run the `deploy-cluster` script (`./deploy-cluster`)

This script will:

### Create the ubuntu vms with multipass


* Create and start 3 vms (with names `control-plane-node`, `kube-node-1`, `kube-node-2`)
* Add the public key `./keys/key.pub` in each vm's `authorized_keys`
* Create the ansible `inventory` file by getting each vm's ip.


### Create and configure kubernetes with cluster with ansible using the below playbooks

* `./bin/install-ansible-collections-and-roles` Installs ansible collections and roles.
* `./bin/ansible-playbook 00-prequirements.yml` Installs basic configuration requirements for each node.
* `./bin/ansible-playbook 01-crio.yml` Installs [cri-o](https://cri-o.io/) as [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
* `./bin/ansible-playbook 02-kube-install.yml` Installs kubernetes deployment tools ([kubeadm, kubelet, kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)).
* `./bin/ansible-playbook 03-kube-cluster.yml` Setups the kubernetes cluster and installs [Calico](https://projectcalico.docs.tigera.io/about/about-calico) networking.


_______________________

## How to Access cluster

### A) From host (get .kube/config from `control-plane-node`)
You can access the cluster directly with `kubectl` from the host machine by getting the `~/.kube/config` from `control-plane-node`
```bash
kubectl get nodes
```
### B) From kube-node-0 (`multipass shell kube-node-0`)
You can access kubernetes cluster through `kube-node-0` by opening a shell to it with `multipass shell control-plane-node`.
```bash
multipass shell control-plane-node
kubectl get nodes
```
