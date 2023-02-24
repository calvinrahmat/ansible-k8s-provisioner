# Provision high availability kubernetes cluster

Provision high availability [Kubernetes](https://kubernetes.io) cluster on Linux.

| ![space-1.jpg](https://user-images.githubusercontent.com/83713045/220837894-af06f536-5a08-43df-ac35-bf47fef9eeae.png) | 
|:--:| 
| From: *https://blog.ajiarya.id/posts/kubernetes/cara-menyiapkan-high-availability-kubernetes-klaster-kubeadm/* |

## Tested on
- Ubuntu 20.04

## Requirements
- Internet Connection
- Unreserved IP address for [Virtual IP Address (VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)
- SSH connection for each node

## Usage
### 1. Register all your IP address or hostname that you want to be provisioned to become kubernetes cluster in invetory file `inventory`
### 2. Edit the variables in each role based on your environment
### 3. Update variable in each role please read the [Roles](#roles) Section
### 4. Install the "community.general" collection from Ansible Galaxy.
```
ansible-galaxy collection install community.general      
```
### 5. *(optional)* Run the bootstrap.yml file. This file will update and upgrade packages of all your nodes
```
ansible-playbook bootstrap.yml
```
### 6. Run the install-k8s-ha.yml to deploy the high availability kubernetes cluster
```
ansible-playbook install-k8s-ha.yml
```

## Roles

There are some roles to provision the high availability kubernetes cluster:

### 1. ca_generator 

*(optional)* This role is to generate a custom CA. It will be used on kubeadm. You need to adjust the variable in `roles/ca_generator/defaults/main.yml`

### 2. containerd

This role is to provision [CONTAINERD](https://containerd.io/) in each node based on inventory file.

### 3. crio-container

This role is to prision [CRI-O](https://cri-o.io/) container in each node based on inventory file.

### 4. etc_hosts_update

This role is to update ip address and hostname to /etc/hosts in each node based on inventory file.

### 5. haproxy-keepalived

This role is to deploy [HAproxy](https://www.haproxy.org/) and [keepalived](https://keepalived.readthedocs.io/en/latest/introduction.html). These applications will be act as Load Balancers. The haproxy and keepalived default configuration are available on `roles/haproxy-keepalived/templates`. You also need to adjust the variable `roles/haproxy-keepalived/defaults/`
```yaml
keepalived_vip: 192.168.1.100/24 # Update vip address according to your environment
keepalived_auth_pass: nasgorenak # Update your auth pass 
```

### 6. k8s_deploy

This role is to install kubernetes in each nodes (master and worker nodes). You need to ajust environment variable on `roles/k8s_deploy/defaults/main.yml` 
```yaml
k8s_version: 1.25.6-00 # Select your desired kubernetes cluster
kubernetes_vip_address: 192.168.1.100 # Update your vip address
```

### 7. k8s_init

This role is bootstrap or initialized the kubernetes cluster and also create the join command for master and worker nodes. You can select CNI for your kubernetes cluster [Flannel](https://github.com/flannel-io/flannel) or [Calico](https://docs.tigera.io/calico/3.25/reference/configure-cni-plugins). You need to adjust the cidr address variable on `roles/k8s_init/defaults/main.yml`

### 8. k8s_join_masters

This role is add new master node to your existing kubernetes cluster

### 9. k8s_join_workers

This role is add new worker node to your existing kubernetes cluster

## References

- https://blog.ajiarya.id/posts/kubernetes/cara-menyiapkan-high-availability-kubernetes-klaster-kubeadm/
- https://docs.ansible.com/

