# Session 3: Kubernetes Cluster Quick Setup Using Vagrant

## Easy & Fast Kubernetes Deployment – Detailed Study Notes

---

# 1. Session Overview

### Objective

This session focuses on:

* Quick Kubernetes Cluster Setup
* Using **Vagrant + Oracle VirtualBox**
* Automated Kubernetes Deployment
* Understanding Master and Worker Nodes
* Understanding ETCD Quorum Concept
* Kubernetes Control Plane Components
* Kubernetes Cluster Scalability Limits

### Why This Session Is Important?

Normally Kubernetes installation requires:

* Multiple Linux Machines
* Manual Configuration
* Network Setup
* Kubernetes Components Installation

Using **Vagrant**, all of this becomes automated and much faster.

---

# 2. Kubernetes Cluster Architecture

A Kubernetes Cluster consists of:

```text
Kubernetes Cluster
│
├── Master Nodes (Control Plane)
│
└── Worker Nodes
```

### Master Nodes

Also called:

* Control Plane Nodes

Responsibilities:

* Cluster Management
* Scheduling
* API Handling
* State Management

### Worker Nodes

Responsibilities:

* Running Applications
* Running Containers
* Executing Pods

---

# 3. How Many Master Nodes Should Be Used?

Common configurations:

| Master Nodes | Recommended      |
| ------------ | ---------------- |
| 1            | Development/Lab  |
| 3            | Production       |
| 5            | Large Production |
| 7            | Enterprise Scale |

---

## Important Rule

Always use **Odd Number of Master Nodes**

Examples:

✅ 1

✅ 3

✅ 5

✅ 7

❌ 2

❌ 4

❌ 6

---

# 4. Why Odd Number of Master Nodes?

Because Kubernetes ETCD uses:

### Raft Consensus Algorithm

It requires:

### Quorum

Quorum Formula:

```text
Quorum = (N/2) + 1
```

Where:

```text
N = Total ETCD Nodes
```

---

## Example

### 3 ETCD Nodes

```text
Quorum = (3/2) + 1
       = 2
```

Need at least:

```text
2 Nodes Agreement
```

---

### 5 ETCD Nodes

```text
Quorum = (5/2) + 1
       = 3
```

Need:

```text
3 Nodes Agreement
```

---

# 5. Understanding Quorum with Election Example

Imagine:

```text
Total Seats = 40
```

Winning Majority:

```text
40/2 + 1
= 21
```

A party needs:

```text
21 seats
```

to form a government.

Similarly:

Kubernetes requires majority agreement among ETCD members.

---

# 6. What Is Quorum?

### Definition

Minimum number of ETCD members required to agree before an operation is considered valid.

Used for:

* Data Consistency
* Cluster Reliability
* Fault Tolerance

---

# 7. Why Quorum Is Important?

## 1. Prevents Split-Brain

Split-brain means:

Two groups think they are the valid cluster.

Example:

```text
4 Nodes

2 Say YES
2 Say NO
```

No clear majority.

Creates conflict.

---

## 2. Maintains Strong Consistency

All nodes see the same cluster state.

---

## 3. Prevents Data Corruption

Avoids inconsistent writes.

Ensures reliable cluster operation.

---

# 8. Fault Tolerance Based on Master Nodes

| Master Nodes | Quorum | Failures Tolerated |
| ------------ | ------ | ------------------ |
| 1            | 1      | 0                  |
| 3            | 2      | 1                  |
| 5            | 3      | 2                  |
| 7            | 4      | 3                  |

---

## Example

### 3 Masters

```text
Master1
Master2
Master3
```

If:

```text
Master3 Fails
```

Remaining:

```text
Master1
Master2
```

Quorum still achieved.

Cluster continues working.

---

# 9. Resource Requirements for Practice

Minimum recommended per VM:

```text
CPU : 1 Core
RAM : 2 GB
```

---

## Small Lab Setup

```text
1 Master
2 Worker
```

or

```text
1 Master
3 Worker
```

---

## Larger Lab Setup

If system has sufficient resources:

```text
3 Masters
5 Workers
```

---

# 10. Why Use Vagrant?

Beginners often struggle with:

* Manual Linux Installation
* VM Creation
* Kubernetes Setup
* Networking Configuration

Vagrant automates all these tasks.

Benefits:

✔ Fast Deployment

✔ Easy Setup

✔ Repeatable Environment

✔ Good for Learning

---

# 11. Kubernetes Cluster Definition

A Cluster is:

> A group of physical or virtual machines running Kubernetes components and managed together.

Nodes can be:

* Physical Servers
* Virtual Machines

---

# 12. Kubernetes Scalability Limits

According to Kubernetes design limits:

| Resource         | Limit   |
| ---------------- | ------- |
| Nodes            | 5000    |
| Pods per Node    | 110     |
| Total Pods       | 150,000 |
| Total Containers | 300,000 |

---

## Important Limits

### Pods Per Node

```text
110 Pods
```

maximum on a single node.

---

### Maximum Nodes

```text
5000 Nodes
```

per cluster.

---

### Maximum Pods

```text
150,000 Pods
```

per cluster.

---

### Maximum Containers

```text
300,000 Containers
```

per cluster.

---

# 13. Required Software

Before creating the cluster install:

## 1. Oracle VirtualBox

Used for:

* Virtual Machine Creation
* Local Lab Environment

---

## 2. VirtualBox Extension Pack

Provides additional functionality:

* Better Hardware Support
* Enhanced Virtualization Features

---

## 3. Vagrant

Used for:

* VM Automation
* Infrastructure as Code
* Cluster Provisioning

---

# 14. Installation Workflow

## Step 1

Install:

```text
Oracle VirtualBox
```

---

## Step 2

Install:

```text
VirtualBox Extension Pack
```

---

## Step 3

Install:

```text
Vagrant
```

---

## Step 4

Reboot the machine.

---

# 15. Getting Kubernetes Vagrant Project

Options:

### Method 1

Clone Repository

```bash
git clone <repository-url>
```

---

### Method 2

Download ZIP

```text
Download ZIP
Extract ZIP
```

---

# 16. Important Directory

Inside extracted project:

```text
setup-of-k8s/
│
└── vagrant-kubeadm/
    │
    └── Vagrantfile
```

---

# 17. What Is Vagrantfile?

The most important file.

Contains:

* VM Definitions
* Master Node Count
* Worker Node Count
* CPU Settings
* Memory Settings
* Provisioning Scripts

---

## Example Configuration

### One Master

```text
Master Nodes = 1
```

---

### Three Masters

```text
Master Nodes = 3
```

---

### Workers

```text
Worker Nodes = 3
```

or

```text
Worker Nodes = 5
```

---

# 18. Recommended Configuration

## Low Resource Machine

```text
1 Master
2-3 Workers
```

---

## 12 GB+ RAM Machine

```text
3 Masters
3-5 Workers
```

---

# 19. Creating the Cluster

Navigate to folder containing:

```text
Vagrantfile
```

Open:

```text
PowerShell
```

or

```text
MobaXterm
```

---

## Run Command

```bash
vagrant up
```

---

# 20. What Happens After Running `vagrant up`?

Vagrant automatically:

### Creates VMs

```text
Master Node
Worker Nodes
```

---

### Installs Kubernetes

Components are installed automatically.

---

### Configures Networking

Nodes communicate automatically.

---

### Joins Nodes to Cluster

Worker nodes become part of cluster.

---

### Final Result

Fully functional Kubernetes cluster.

---

# 21. Common Error During Setup

Sometimes Vagrant may show errors like:

```text
Command Failed
```

or

```text
Provisioning Error
```

---

## Solution

Simply run again:

```bash
vagrant up
```

Vagrant resumes from the failed step and usually fixes the issue automatically.

---

# 22. Kubernetes Control Plane Components

## 1. kube-apiserver

### Role

Front-end of Kubernetes Control Plane.

Responsibilities:

* Receives Requests
* Validates Requests
* Processes Requests
* Communicates with Cluster Components

Sources:

* kubectl
* Dashboard
* APIs

---

## 2. ETCD

### Role

Key-Value Database

Stores:

* Cluster State
* Configuration Data
* Metadata

Known as:

```text
Single Source of Truth
```

for Kubernetes.

---

## 3. kube-scheduler

### Role

Schedules Pods.

Determines:

```text
Which Node Will Run Which Pod
```

Based on:

* CPU Availability
* Memory Availability
* Constraints
* Policies

---

## 4. kube-controller-manager

### Role

Runs Controllers.

Responsibilities:

* Monitor Cluster State
* Compare Current State
* Compare Desired State
* Take Corrective Actions

---

# 23. Worker Node Components

## 1. kubelet

Node Agent.

Responsibilities:

* Communicates with Control Plane
* Starts Containers
* Monitors Pods
* Ensures Desired State

---

## 2. kube-proxy

Responsibilities:

* Network Rules Management
* Service Connectivity
* Routing
* Load Balancing

---

## 3. Container Runtime

Examples:

* Docker
* containerd
* CRI-O

Responsibilities:

* Pull Images
* Start Containers
* Stop Containers
* Manage Container Lifecycle

---

# 24. Manual Installation vs Vagrant Installation

| Manual Setup             | Vagrant Setup        |
| ------------------------ | -------------------- |
| Time Consuming           | Fast                 |
| Complex                  | Easy                 |
| Multiple Steps           | Automated            |
| Good for Deep Learning   | Good for Quick Lab   |
| Production Understanding | Practice Environment |

---

# 25. Key Exam & Interview Questions

### Q1. Why are odd-numbered master nodes preferred?

Because ETCD quorum requires majority consensus and odd numbers avoid split-brain situations.

---

### Q2. What is the quorum formula?

```text
Quorum = (N/2) + 1
```

---

### Q3. What database does Kubernetes use?

```text
ETCD
```

---

### Q4. What is ETCD?

A distributed key-value store used to maintain Kubernetes cluster state.

---

### Q5. What command creates the Kubernetes cluster using Vagrant?

```bash
vagrant up
```

---

### Q6. Which component schedules pods?

```text
kube-scheduler
```

---

### Q7. Which component acts as the front-end of Kubernetes?

```text
kube-apiserver
```

---

### Q8. What is the role of kubelet?

Runs on worker nodes and ensures containers are running correctly.

---

# Session 3 Summary

* Kubernetes cluster consists of Master and Worker nodes.
* Always prefer odd-numbered Control Plane nodes.
* ETCD uses Raft Consensus Algorithm.
* Quorum Formula = (N/2) + 1.
* Vagrant automates Kubernetes cluster creation.
* Required tools:

  * Oracle VirtualBox
  * VirtualBox Extension Pack
  * Vagrant
* Cluster can be created using:

  ```bash
  vagrant up
  ```
* Control Plane Components:

  * kube-apiserver
  * ETCD
  * kube-scheduler
  * kube-controller-manager
* Worker Components:

  * kubelet
  * kube-proxy
  * Container Runtime
* Vagrant is ideal for quickly building a Kubernetes practice environment.
