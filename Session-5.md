# Kubernetes Session 5: Kubeconfig & Cluster Access

## Overview

This session focuses on:

* Accessing a Kubernetes cluster
* Understanding Kubeconfig files
* Managing cluster nodes using `kubectl`
* Understanding important Kubernetes configuration files
* Exploring `/etc/kubernetes` directory structure
* Understanding Manifest and PKI directories
* Node labeling and cluster administration basics

---

# 1. Kubernetes Cluster Setup Overview

## Cluster Architecture Used

| Node Type     | Hostname | Purpose            |
| ------------- | -------- | ------------------ |
| Control Plane | master   | Cluster Management |
| Worker Node   | worker01 | Runs Applications  |
| Worker Node   | worker02 | Runs Applications  |

### Cluster Deployment Components

* VMware Workstation
* kubeadm
* Linux Virtual Machines

All nodes are connected over the same network and communicate with each other.

---

# 2. Verifying Cluster Connectivity

## Check Network Connectivity

```bash
ping worker01
ping worker02
```

or

```bash
ping <IP_ADDRESS>
```

### Successful Ping Confirms

* DNS resolution is working
* Hosts file entries are configured
* Nodes can communicate with each other

---

# 3. Verifying kubectl Installation

## Locate kubectl Binary

```bash
which kubectl
```

### Example Output

```bash
/usr/bin/kubectl
```

### Purpose

* Confirms `kubectl` is installed
* Confirms binary is available in `PATH`

---

# 4. Verifying Kubernetes Cluster Status

## List All Nodes

```bash
kubectl get nodes
```

### Example Output

```text
NAME       STATUS   ROLES           AGE   VERSION
master     Ready    control-plane   10d   v1.30
worker01   Ready    <none>          10d   v1.30
worker02   Ready    <none>          10d   v1.30
```

---

## Understanding Output Columns

### NAME

Hostname of the node.

Examples:

```text
master
worker01
worker02
```

---

### STATUS

Indicates node health.

Possible values:

* Ready
* NotReady
* Unknown

**Ready** means:

* Kubelet is healthy
* Node can schedule pods

---

### ROLES

Indicates node role.

Examples:

* control-plane
* worker

Default worker nodes often show:

```text
<none>
```

This is completely normal.

---

### AGE

Time since node joined the cluster.

Examples:

```text
10d
5h
20m
```

---

### VERSION

Kubelet version running on node.

Example:

```text
v1.30.0
```

---

# 5. Assigning Labels to Worker Nodes

By default worker nodes display:

```text
<none>
```

in the Roles column.

## Label Worker01

```bash
kubectl label node worker01 \
node-role.kubernetes.io/worker=worker1
```

---

## Label Worker02

```bash
kubectl label node worker02 \
node-role.kubernetes.io/worker=worker2
```

---

## Verify Labels

```bash
kubectl get nodes
```

Worker labels will now appear in node metadata.

---

# 6. Getting Detailed Information About Nodes

## Describe Master Node

```bash
kubectl describe node master
```

---

## Describe Worker Nodes

```bash
kubectl describe node worker01
```

or

```bash
kubectl describe node worker02
```

---

## Information Available

### Node Metadata

* Node Name
* Labels
* Annotations
* Creation Time

### Network Information

* Internal IP
* Hostname
* Network Details

### Resource Information

* CPU Capacity
* Memory Capacity
* Storage Capacity

### Allocated Resources

Shows:

* Requested Resources
* Consumed Resources

### System Information

Includes:

* Machine ID
* System UUID
* Boot ID
* OS Version
* Kernel Version

---

# 7. Important Kubernetes Directory

## Location

```bash
/etc/kubernetes
```

This is one of the most critical Kubernetes directories.

### Contains

* Cluster configuration files
* Kubeconfig files
* Certificates
* Security keys
* Control Plane manifests

---

## Directory Structure

Typical contents:

```text
admin.conf
controller-manager.conf
kubelet.conf
scheduler.conf
super-admin.conf
manifests/
pki/
```

---

# 8. Important Kubeconfig Files

## admin.conf

### Purpose

Main kubeconfig file for cluster administrators.

Created automatically during:

```bash
kubeadm init
```

### Used By

```bash
kubectl
```

for cluster administration.

### Contains

* Cluster details
* User information
* Authentication information
* Certificates

---

### Common Practice

Create local kubeconfig:

```bash
mkdir -p ~/.kube

cp /etc/kubernetes/admin.conf ~/.kube/config

chown $(id -u):$(id -g) ~/.kube/config
```

---

### View Contents

```bash
cat /etc/kubernetes/admin.conf
```

Contains:

```yaml
apiVersion:
clusters:
contexts:
users:
certificate-authority-data:
client-certificate-data:
client-key-data:
```

---

### Security Importance

Contains highly sensitive information:

* Client Certificates
* Private Keys
* Authentication Credentials

Only privileged users should access it.

---

## controller-manager.conf

### Used By

```text
kube-controller-manager
```

### Purpose

Authenticate with the Kubernetes API Server.

---

## scheduler.conf

### Used By

```text
kube-scheduler
```

### Purpose

Secure communication with the Kubernetes API Server.

---

## kubelet.conf

### Used By

```text
kubelet
```

### Purpose

Allows worker nodes to communicate with the API Server.

---

## super-admin.conf

Advanced administrative kubeconfig.

Provides elevated administrative access.

---

# 9. Manifest Directory

## Location

```bash
/etc/kubernetes/manifests
```

---

## Contents

```text
etcd.yaml
kube-apiserver.yaml
kube-controller-manager.yaml
kube-scheduler.yaml
```

These are Static Pod definitions.

---

## Static Pods

Managed directly by:

```text
Kubelet
```

Not managed through Deployments or ReplicaSets.

---

# 10. Manifest Files Explained

## kube-apiserver.yaml

Starts:

```text
Kubernetes API Server
```

### Responsibilities

* Central communication hub
* Receives kubectl requests
* Communicates with etcd

---

## kube-controller-manager.yaml

Starts:

```text
Controller Manager
```

### Responsibilities

* Node Controller
* ReplicaSet Controller
* Endpoint Controller
* Namespace Controller

---

## kube-scheduler.yaml

Starts:

```text
Scheduler
```

### Responsibilities

* Pod Placement
* Resource Allocation
* Scheduling Decisions

---

## etcd.yaml

Starts:

```text
etcd Database
```

### Responsibilities

* Stores cluster state
* Stores configurations
* Stores secrets
* Stores metadata

---

# 11. PKI Directory

## Location

```bash
/etc/kubernetes/pki
```

### PKI

**Public Key Infrastructure**

Contains:

* TLS Certificates
* Private Keys
* CA Certificates

---

## Why PKI is Important

Provides:

* Encryption
* Authentication
* Secure communication

between Kubernetes components.

---

# 12. Important PKI Files

## ca.crt

Certificate Authority Certificate.

Used to verify certificates.

---

## ca.key

Certificate Authority Private Key.

Used to sign certificates.

> ⚠️ Extremely sensitive.

---

## apiserver.crt

API Server TLS Certificate.

---

## apiserver.key

API Server Private Key.

---

## apiserver-kubelet-client.crt

Used by API Server to communicate securely with Kubelet.

---

## apiserver-kubelet-client.key

Private key used for kubelet communication.

---

## etcd Directory

Contains:

* ETCD TLS Certificates
* ETCD Private Keys

Used for secure ETCD communication.

---

## front-proxy Files

Used by:

```text
API Aggregation Layer
```

for authentication and proxy communication.

---

# 13. Security Best Practices

## Protect Kubeconfig Files

```bash
chmod 600 ~/.kube/config
```

---

## Restrict Access

Only:

* root
* Kubernetes administrators

should access configuration files.

---

## Backup Critical Directories

Always backup:

```bash
/ etc/kubernetes
/ etc/kubernetes/pki
```

---

## Never Share

Do **NOT** share:

* admin.conf
* ca.key
* apiserver.key
* kubeconfig files

These files can provide cluster-admin access.

---

# Key kubectl Commands Summary

## Locate kubectl Binary

```bash
which kubectl
```

---

## List Cluster Nodes

```bash
kubectl get nodes
```

---

## Detailed Node Information

```bash
kubectl describe node <node-name>
```

---

## Assign Worker Label

```bash
kubectl label node <node-name> \
node-role.kubernetes.io/worker=worker
```

---

## List Namespaces

```bash
kubectl get namespaces
```

---

## List Services Across Namespaces

```bash
kubectl get services --all-namespaces
```

---

# Interview Questions

## Q1. What is admin.conf?

**Answer:**

A kubeconfig file generated by `kubeadm init` that allows cluster administrators to access and manage the Kubernetes cluster.

---

## Q2. What is the purpose of kubelet.conf?

**Answer:**

Allows Kubelet to authenticate and communicate with the Kubernetes API Server.

---

## Q3. What are Static Pods?

**Answer:**

Pods managed directly by Kubelet using manifest files located in:

```bash
/etc/kubernetes/manifests
```

---

## Q4. What is stored in etcd?

**Answer:**

Cluster state information including:

* Pods
* Nodes
* ConfigMaps
* Secrets
* Deployments
* Cluster Metadata

---

## Q5. Why is the PKI directory important?

**Answer:**

It contains TLS certificates and private keys required for secure communication between Kubernetes components.

---

# Session Takeaway

After completing this session, you should understand:

* ✅ How to verify cluster health using `kubectl`
* ✅ How to view and manage Kubernetes nodes
* ✅ How node labeling works
* ✅ Purpose of Kubeconfig files
* ✅ Structure of `/etc/kubernetes`
* ✅ Static Pod manifests
* ✅ PKI certificates and security
* ✅ Cluster administration fundamentals using `kubectl` and kubeconfig files
