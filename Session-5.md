Kubernetes Session 5 Notes: Kubeconfig & Cluster Access

Overview

This session focuses on:

Accessing a Kubernetes cluster

Understanding Kubeconfig files

Managing cluster nodes using kubectl

Understanding important Kubernetes configuration files

Exploring /etc/kubernetes directory structure

Understanding Manifest and PKI directories

Node labeling and cluster administration basics



---

1. Kubernetes Cluster Setup Overview

Cluster Architecture Used

Node Type	Hostname	Purpose

Control Plane	master	Cluster Management
Worker Node	worker01	Runs Applications
Worker Node	worker02	Runs Applications


The cluster was deployed using:

VMware Workstation

kubeadm

Linux VMs


All nodes are connected over the same network and communicate with each other.


---

2. Verifying Cluster Connectivity

Check Network Connectivity

ping worker01
ping worker02

or

ping <IP_ADDRESS>

Successful ping confirms:

DNS resolution is working

Hosts file entries are configured

Nodes can communicate



---

3. Verifying kubectl Installation

Locate kubectl Binary

which kubectl

Example Output:

/usr/bin/kubectl

Purpose:

Confirms kubectl is installed.

Confirms binary is available in PATH.



---

4. Verifying Kubernetes Cluster Status

List All Nodes

kubectl get nodes

Example Output:

NAME       STATUS   ROLES           AGE   VERSION
master     Ready    control-plane   10d   v1.30
worker01   Ready    <none>          10d   v1.30
worker02   Ready    <none>          10d   v1.30


---

Understanding Output Columns

NAME

Hostname of the node.

Example:

master
worker01
worker02


---

STATUS

Indicates node health.

Possible values:

Ready
NotReady
Unknown

Ready means:

Kubelet is healthy.

Node can schedule pods.



---

ROLES

Indicates node role.

Example:

control-plane
worker

Default worker nodes often show:

<none>

which is normal.


---

AGE

Time since node joined cluster.

Example:

10d
5h
20m


---

VERSION

Kubelet version running on node.

Example:

v1.30.0


---

5. Assigning Labels to Worker Nodes

By default worker nodes show:

<none>

in role column.

We can assign labels.


---

Label Worker01

kubectl label node worker01 \
node-role.kubernetes.io/worker=worker1


---

Label Worker02

kubectl label node worker02 \
node-role.kubernetes.io/worker=worker2


---

Verify Labels

kubectl get nodes

Output:

worker

appears in role section.


---

6. Getting Detailed Information About Nodes

Describe Master Node

kubectl describe node master


---

Describe Worker Node

kubectl describe node worker01

or

kubectl describe node worker02


---

Information Available

Node Metadata

Node Name

Labels

Annotations

Creation Time


Network Information

Internal IP

Hostname

Network Details


Resource Information

CPU Capacity

Memory Capacity

Storage


Allocated Resources

Shows:

Requested Resources

Consumed Resources


System Information

Includes:

Machine ID

System UUID

Boot ID

OS Version

Kernel Version



---

7. Important Kubernetes Directory

Location

/etc/kubernetes

This is one of the most critical Kubernetes directories.

Contains:

Cluster configuration files

Kubeconfig files

Certificates

Security keys

Control Plane manifests



---

Directory Structure

Typical contents:

admin.conf
controller-manager.conf
kubelet.conf
scheduler.conf
super-admin.conf
manifests/
pki/


---

8. Important Kubeconfig Files


---

admin.conf

Purpose

Main kubeconfig file for cluster administrators.

Created automatically during:

kubeadm init


---

Usage

Used by:

kubectl

for cluster administration.

Contains:

Cluster details

User information

Authentication information

Certificates



---

Common Practice

Copy admin.conf to user kubeconfig location:

mkdir -p ~/.kube

cp /etc/kubernetes/admin.conf ~/.kube/config

Set permissions:

chown $(id -u):$(id -g) ~/.kube/config


---

View Contents

cat /etc/kubernetes/admin.conf

Contains:

apiVersion:
clusters:
contexts:
users:
certificate-authority-data:
client-certificate-data:
client-key-data:


---

Security Importance

Contains sensitive data:

Client Certificates

Private Keys

Authentication Tokens


Only privileged users should access it.


---

controller-manager.conf

Used by:

kube-controller-manager

Purpose:

Authenticate with API Server.


---

scheduler.conf

Used by:

kube-scheduler

Purpose:

Connect to API Server securely.


---

kubelet.conf

Used by:

kubelet

Purpose:

Allow worker nodes to communicate with API Server.


---

super-admin.conf

Advanced administrative kubeconfig.

Provides elevated administrative access.


---

9. Manifest Directory

Location

/etc/kubernetes/manifests


---

Contents

etcd.yaml
kube-apiserver.yaml
kube-controller-manager.yaml
kube-scheduler.yaml

These are Static Pod definitions.


---

Static Pods

Managed directly by:

Kubelet

Not managed through Deployments.


---

10. Manifest Files Explained


---

kube-apiserver.yaml

Starts:

Kubernetes API Server

Role:

Central communication hub

Receives kubectl requests

Communicates with etcd



---

kube-controller-manager.yaml

Starts:

Controller Manager

Responsibilities:

Node Controller

ReplicaSet Controller

Endpoint Controller

Namespace Controller



---

kube-scheduler.yaml

Starts:

Scheduler

Responsibilities:

Pod placement

Resource allocation

Scheduling decisions



---

etcd.yaml

Starts:

etcd Database

Responsibilities:

Stores cluster state

Stores configurations

Stores secrets and metadata



---

11. PKI Directory

Location

/etc/kubernetes/pki

PKI = Public Key Infrastructure

Contains:

TLS Certificates

Private Keys

CA Certificates



---

Why PKI is Important

Provides:

Encryption

Authentication

Secure communication


between Kubernetes components.


---

12. Important PKI Files

ca.crt

Certificate Authority Certificate

Used to verify certificates.


---

ca.key

Certificate Authority Private Key

Used to sign certificates.

⚠️ Extremely sensitive.


---

apiserver.crt

API Server TLS Certificate.


---

apiserver.key

API Server Private Key.


---

apiserver-kubelet-client.crt

Used by API Server to communicate with Kubelet.


---

apiserver-kubelet-client.key

Private key for kubelet communication.


---

etcd Directory

Contains:

ETCD TLS certificates
ETCD private keys

Used for secure etcd communication.


---

front-proxy Files

Used by:

API Aggregation Layer

for authentication and proxy communication.


---

13. Security Best Practices

Protect Kubeconfig Files

chmod 600 ~/.kube/config


---

Restrict Access

Only:

root

Kubernetes administrators


should access configuration files.


---

Backup Critical Directories

Always backup:

/etc/kubernetes
/etc/kubernetes/pki


---

Never Share

Do NOT share:

admin.conf

ca.key

apiserver.key

kubeconfig files


These can provide cluster-admin access.


---

Key kubectl Commands Summary

which kubectl

Locate kubectl binary.


---

kubectl get nodes

List cluster nodes.


---

kubectl describe node <node-name>

Detailed node information.


---

kubectl label node <node-name> \
node-role.kubernetes.io/worker=worker

Assign worker label.


---

kubectl get namespaces

List namespaces.


---

kubectl get services --all-namespaces

List services across namespaces.


---

Interview Questions

Q1. What is admin.conf?

A kubeconfig file generated by kubeadm init that allows cluster administrators to access and manage the Kubernetes cluster.


---

Q2. What is the purpose of kubelet.conf?

Allows Kubelet to authenticate and communicate with the Kubernetes API Server.


---

Q3. What are Static Pods?

Pods managed directly by Kubelet using manifest files located in:

/etc/kubernetes/manifests


---

Q4. What is stored in etcd?

Cluster state information:

Pods

Nodes

ConfigMaps

Secrets

Deployments

Cluster metadata



---

Q5. Why is the PKI directory important?

It contains TLS certificates and keys required for secure communication between Kubernetes components.


---

Session Takeaway

After this session you should understand:

✅ How to verify cluster health using kubectl

✅ How to view and manage Kubernetes nodes

✅ How node labeling works

✅ Purpose of Kubeconfig files

✅ Structure of /etc/kubernetes

✅ Static Pod manifests

✅ PKI certificates and security

✅ Cluster administration fundamentals using kubectl and kubeconfig files.