# Session 4 Notes: Build a Kubernetes Cluster on RHEL 9 in VMware (Manual Setup)

## Overview

In this session, a complete Kubernetes cluster is manually configured on RHEL 9 machines running inside VMware.

### Cluster Architecture

A Kubernetes cluster consists of:

* **Control Plane (Master Node)**
* **Worker Nodes**

### Lab Setup Used

| Node          | Hostname             | IP Address      | Role          |
| ------------- | -------------------- | --------------- | ------------- |
| Master Node   | master01.classes.com | 192.168.229.137 | Control Plane |
| Worker Node 1 | worker01.classes.com | 192.168.229.139 | Worker        |
| Worker Node 2 | worker02.classes.com | 192.168.229.141 | Worker        |

---

# Prerequisites

Before starting Kubernetes installation:

### Operating System

* RHEL 9.x

### Access Requirements

* SSH Access
* Root or Sudo Privileges

### Hardware Requirements

Minimum:

* 2 GB RAM
* 2 vCPU
* 20 GB Disk

Recommended:

* 4 GB RAM
* 4 vCPU
* 40 GB Disk

### Network Requirements

* Stable internet connection
* All nodes should communicate with each other

---

# Step 1: Configure Hostnames

## Master Node

```bash
hostnamectl set-hostname master01.classes.com
```

Verify:

```bash
hostnamectl
```

---

## Worker Node 1

```bash
hostnamectl set-hostname worker01.classes.com
```

---

## Worker Node 2

```bash
hostnamectl set-hostname worker02.classes.com
```

---

# Step 2: Configure /etc/hosts

Edit the hosts file on **all nodes**:

```bash
vi /etc/hosts
```

Add:

```bash
192.168.229.137 master01.classes.com
192.168.229.139 worker01.classes.com
192.168.229.141 worker02.classes.com
```

Verify connectivity:

```bash
ping worker01.classes.com
ping worker02.classes.com
ping master01.classes.com
```

---

# Step 3: Disable Swap

Kubernetes requires swap to be disabled.

## Disable Swap Temporarily

```bash
swapoff -a
```

---

## Disable Swap Permanently

Edit:

```bash
vi /etc/fstab
```

Comment swap entry:

```bash
#/dev/mapper/rhel-swap swap swap defaults 0 0
```

OR

```bash
sed -i '/swap/s/^/#/' /etc/fstab
```

Verify:

```bash
free -h
```

Swap should be 0.

---

# Step 4: Configure SELinux

## Set SELinux to Permissive Mode

Temporary:

```bash
setenforce 0
```

---

Permanent:

```bash
vi /etc/selinux/config
```

Change:

```bash
SELINUX=enforcing
```

To:

```bash
SELINUX=permissive
```

---

# Step 5: Configure Repositories

## Enable EPEL Repository

Install EPEL:

```bash
dnf install epel-release -y
```

Verify:

```bash
dnf repolist all
```

---

# Step 6: Load Required Kernel Modules

Load modules:

```bash
modprobe overlay
modprobe br_netfilter
```

---

## Make Modules Persistent

Create file:

```bash
vi /etc/modules-load.d/k8s.conf
```

Add:

```text
overlay
br_netfilter
```

---

# Step 7: Configure Kernel Parameters

Create file:

```bash
vi /etc/sysctl.d/k8s.conf
```

Add:

```text
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
```

Apply settings:

```bash
sysctl --system
```

Verify:

```bash
sysctl net.ipv4.ip_forward
```

---

# Step 8: Configure Firewall Rules

---

## Master Node Ports

Allow:

| Port      | Protocol | Purpose            |
| --------- | -------- | ------------------ |
| 6443      | TCP      | API Server         |
| 2379-2380 | TCP      | etcd               |
| 10250     | TCP      | Kubelet            |
| 10251     | TCP      | Scheduler          |
| 10252     | TCP      | Controller Manager |
| 10257     | TCP      | Controller Manager |
| 10259     | TCP      | Scheduler          |
| 179       | TCP      | Calico BGP         |
| 4789      | UDP      | VXLAN              |

Example:

```bash
firewall-cmd --permanent --add-port=6443/tcp
```

Repeat for all required ports.

Reload:

```bash
firewall-cmd --reload
```

Verify:

```bash
firewall-cmd --list-all
```

---

## Worker Node Ports

Allow:

| Port        | Protocol |
| ----------- | -------- |
| 10250       | TCP      |
| 30000-32767 | TCP      |
| 179         | TCP      |
| 4789        | UDP      |

Reload:

```bash
firewall-cmd --reload
```

---

# Step 9: Install Container Runtime (containerd)

Kubernetes needs a Container Runtime Interface (CRI).

### Add Docker Repository

```bash
dnf config-manager \
--add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
```

Run on all nodes.

---

### Install Containerd

```bash
dnf install containerd.io -y
```

---

### Enable Service

```bash
systemctl enable containerd --now
```

Verify:

```bash
systemctl status containerd
```

---

# Step 10: Configure Containerd

Generate default configuration:

```bash
mkdir -p /etc/containerd
containerd config default > /etc/containerd/config.toml
```

---

Edit:

```bash
vi /etc/containerd/config.toml
```

Find:

```text
SystemdCgroup = false
```

Change to:

```text
SystemdCgroup = true
```

---

Restart containerd:

```bash
systemctl restart containerd
```

---

# Why SystemdCgroup = true?

Kubernetes uses systemd for resource management.

Benefits:

* Better resource allocation
* Better stability
* Recommended by Kubernetes

---

# Step 11: Add Kubernetes Repository

Create:

```bash
vi /etc/yum.repos.d/kubernetes.repo
```

Add repository configuration.

Important:

```text
exclude=kubelet kubeadm kubectl cri-tools
```

This prevents unintended package upgrades.

---

# Step 12: Install Kubernetes Components

Install on all nodes:

```bash
dnf install -y kubelet kubeadm kubectl
```

Enable kubelet:

```bash
systemctl enable kubelet
```

---

# Kubernetes Components Explained

## kubelet

Runs on every node.

Responsibilities:

* Starts Pods
* Reports node status
* Communicates with Control Plane

---

## kubeadm

Used to:

* Initialize cluster
* Join nodes
* Upgrade cluster

---

## kubectl

Command line tool used to manage Kubernetes.

Examples:

```bash
kubectl get nodes
kubectl get pods
kubectl get svc
```

---

# Step 13: Initialize Kubernetes Cluster

Run ONLY on Master Node:

```bash
kubeadm init
```

This command:

* Creates Control Plane
* Generates certificates
* Configures API Server
* Generates worker join command

---

# Step 14: Configure kubectl Access

Run on Master Node:

```bash
mkdir -p $HOME/.kube

cp -i /etc/kubernetes/admin.conf \
$HOME/.kube/config

chown $(id -u):$(id -g) \
$HOME/.kube/config
```

---

# Step 15: Join Worker Nodes

After initialization Kubernetes displays a join command:

Example:

```bash
kubeadm join 192.168.229.137:6443 \
--token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>
```

Run this command on:

* Worker Node 1
* Worker Node 2

---

# Step 16: Install Calico CNI

Kubernetes needs networking between pods.

Install Calico:

```bash
kubectl apply -f calico.yaml
```

Benefits:

* Pod Networking
* Network Policies
* Scalability
* BGP Support

---

# Step 17: Verify Cluster Health

## Check Pods

```bash
kubectl get pods -A
```

Expected:

All pods should be:

```text
Running
```

---

## Check Nodes

```bash
kubectl get nodes
```

Expected:

```text
master01 Ready
worker01 Ready
worker02 Ready
```

---

# Step 18: Deploy Test Application

Deploy NGINX:

```bash
kubectl create deployment nginx-demo \
--image=nginx
```

---

Scale Deployment

```bash
kubectl scale deployment nginx-demo \
--replicas=2
```

---

Expose Service

```bash
kubectl expose deployment nginx-demo \
--port=80 \
--type=NodePort
```

---

# Step 19: Verify Service

Check service:

```bash
kubectl get svc
```

Example Output:

```text
NAME         TYPE       CLUSTER-IP
nginx-demo   NodePort   10.x.x.x
```

---

# Step 20: Access Application

Use:

```text
http://WorkerNodeIP:NodePort
```

Example:

```text
http://192.168.229.139:30080
```

OR

```text
http://192.168.229.141:30080
```

Expected Page:

```text
Welcome to nginx!
```

---

# Useful Validation Commands

### View Nodes

```bash
kubectl get nodes
```

---

### View Pods

```bash
kubectl get pods
```

---

### View Pods on Specific Nodes

```bash
kubectl get pods -o wide
```

---

### View Services

```bash
kubectl get svc
```

---

### View All Namespaces

```bash
kubectl get ns
```

---

### View Cluster Information

```bash
kubectl cluster-info
```

---

# Common Troubleshooting Commands

Check kubelet:

```bash
systemctl status kubelet
```

Logs:

```bash
journalctl -u kubelet -xe
```

---

Check containerd:

```bash
systemctl status containerd
```

---

Check Pods:

```bash
kubectl describe pod <pod-name>
```

---

Check Events:

```bash
kubectl get events
```

---

# Complete Installation Flow (Quick Revision)

```text
1. Set Hostnames
2. Configure /etc/hosts
3. Disable Swap
4. Configure SELinux
5. Enable EPEL Repo
6. Load Kernel Modules
7. Configure Sysctl Parameters
8. Configure Firewall
9. Install Containerd
10. Configure Containerd
11. Add Kubernetes Repository
12. Install kubelet kubeadm kubectl
13. kubeadm init
14. Configure kubectl
15. Join Worker Nodes
16. Install Calico
17. Verify Cluster
18. Deploy NGINX
19. Expose Service
20. Access Application
```

## Key Takeaway

By the end of this session, a fully functional **3-node Kubernetes cluster (1 Master + 2 Workers)** was successfully built on **RHEL 9 running in VMware**, validated using an **NGINX deployment**, and verified through Kubernetes networking and service exposure. This is the foundational production-style manual installation process that helps understand how Kubernetes components work together behind the scenes.
