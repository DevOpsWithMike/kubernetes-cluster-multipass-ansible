# How to deploy a Kubernetes cluster with Kubeadm, multipass and Ansible
Create a kubernetes cluster from scratch with [multipass](https://multipass.run/install) virtual machines and [ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).



To create the vms run the `deploy-cluster` script (`./deploy-cluster`)

This script will perform the below steps:

1) Create the ubuntu vms with multipass

1.1) Create and start 3 vms (with names `control-plane-node`, `kube-node-1`, `kube-node-2`)
1.2) Add the public key `./keys/key.pub` in each vm's `authorized_keys`
1.3) Create the ansible `inventory` file by getting each vm's ip.


2) Create and configure Kubernetes cluster with ansible using the following playbooks

2.1) `./bin/install-ansible-collections-and-roles` Installs ansible collections and roles.
2.2) `./bin/ansible-playbook 00-prequirements.yml` Installs basic configuration requirements for each node.
2.3) `./bin/ansible-playbook 01-crio.yml` Installs [cri-o](https://cri-o.io/) as [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
2.4) `./bin/ansible-playbook 02-kube-install.yml` Installs kubernetes deployment tools ([kubeadm, kubelet, kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)).
2.5) `./bin/ansible-playbook 03-kube-cluster.yml` Setups the kubernetes cluster and installs [Calico](https://projectcalico.docs.tigera.io/about/about-calico) networking.


______________________________________

## How to access cluster

### A) From host 
You can access the cluster directly with `kubectl`, the script took the .kube/config from `control-plane-node`
```bash
kubectl get nodes
```
### B) From control-plane-node (`multipass shell control-plane-node`)
You can access kubernetes cluster through `control-plane-node` by opening a shell to it with `multipass shell control-plane-node`.
```bash
multipass shell control-plane-node
kubectl get nodes
```
