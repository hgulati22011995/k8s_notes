# Kubernetes Training – Session 2

# Introduction to Kubernetes | Benefits, Problems Solved & Architecture

---

# 1. What is Kubernetes?

**Kubernetes (K8s)** is an **open-source container orchestration platform** used to automate the deployment, scaling, management, and monitoring of containerized applications.

### Definition

> Kubernetes is a container orchestration tool that helps manage and automate containers across multiple servers.

### Why Kubernetes?

Containers are lightweight packages containing:

* Application Code
* Libraries
* Dependencies
* Runtime Environment
* Configuration Files

Kubernetes helps manage these containers efficiently across multiple machines.

---

# 2. Meaning of Container Orchestration

## Orchestration = Management + Automation + Coordination

Kubernetes performs orchestration by:

* Deploying containers
* Starting/Stopping containers
* Scaling applications
* Monitoring health
* Managing networking
* Handling failures automatically

---

# Orchestra Example (Real-Life Analogy)

Imagine an orchestra:

* Musicians = Containers
* Instruments = Applications
* Orchestra Conductor = Kubernetes

Without a conductor:

❌ Noise
❌ No coordination

With a conductor:

✅ Synchronization
✅ Proper timing
✅ Organized execution

Similarly, Kubernetes coordinates all containers inside a cluster.

---

# Other Orchestration Examples

### Event Manager

Coordinates:

* Catering
* Decoration
* Photography
* Music

### Company Manager

Coordinates:

* Employees
* Tasks
* Resources

### Team Captain

Coordinates:

* Team Members
* Strategies
* Execution

Kubernetes performs the same role for containers.

---

# 3. What Kubernetes Manages

Kubernetes controls and coordinates:

### Applications

Running workloads

### Containers

Application runtime units

### Pods

Collection of one or more containers

### Networks

Communication between containers

### Storage

Persistent application data

---

# Important Interview Question

## What is a Pod?

A **Pod** is the smallest deployable unit in Kubernetes.

### Definition

> Pod = Collection of one or more containers

Example:

```
Pod
 ├── Container 1
 └── Container 2
```

---

# 4. Shipping Company Analogy

Imagine:

### Components Mapping

| Shipping Company | Kubernetes |
| ---------------- | ---------- |
| Package          | Container  |
| Truck            | Server     |
| Dispatch Manager | Kubernetes |
| Warehouse        | Cluster    |

### Responsibilities of Kubernetes

* Assign containers to servers
* Prevent overload
* Move workloads if a server fails
* Add more servers when demand increases
* Remove unnecessary resources

---

# 5. Why Kubernetes is Needed

Before Kubernetes, containers were usually run in:

### Standalone Mode

Example:

```
Server
 ├── Docker
 ├── App Container
 ├── Database Container
 └── Cache Container
```

Everything is managed manually.

As the system grows:

* More containers
* More servers
* More complexity

This creates multiple challenges.

---

# 6. Problems Without Kubernetes

---

## 1. No High Availability

### Problem

If a container crashes:

* No automatic recovery
* No failover mechanism

### Result

Manual intervention required.

---

## 2. Manual Scaling

### Problem

Need to manually:

* Add containers
* Remove containers
* Add servers

### Result

Time-consuming and error-prone.

---

## 3. No Horizontal Pod Autoscaling

Without Kubernetes:

* CPU spikes cannot automatically trigger scaling
* Memory usage cannot automatically trigger scaling

Everything must be done manually.

---

## 4. Lack of Self-Healing

### Problem

If a container dies:

```
Container → Crashed
```

Application becomes unavailable.

### Kubernetes Solution

Automatically:

* Restart container
* Replace failed container

---

## 5. No Built-in Service Discovery

### Problem

Must manually manage:

* IP Addresses
* DNS Entries

Container communication becomes difficult.

---

## 6. No Built-in Load Balancing

### Problem

Traffic distribution must be configured manually.

### Result

Uneven resource utilization.

---

## 7. Poor Configuration Management

### Problem

Developers often:

* Hardcode configurations
* Hardcode secrets

### Risks

* Security issues
* Difficult maintenance

---

## 8. No Declarative Infrastructure

Traditional deployment uses:

* Commands
* Scripts

No central configuration file exists.

### Result

Hard to reproduce environments.

---

## 9. Difficult Rollouts & Rollbacks

### Problem

Updating applications is risky.

If deployment fails:

* Rollback must be done manually

### Result

Downtime possible.

---

## 10. No Native Monitoring

Need external setup for:

* Metrics
* Logging
* Health checks

---

## 11. Security & Resource Issues

Problems include:

* CPU misuse
* Memory misuse
* Lack of quotas
* Lack of policy enforcement

---

## 12. Multi-Host Management Complexity

As servers increase:

```
1 Server → Easy
10 Servers → Difficult
100 Servers → Very Difficult
```

Managing:

* Networking
* Security
* Containers

becomes extremely complex.

---

# 7. Benefits of Kubernetes

---

## 1. Centralized Management

Manage everything from a single control plane.

### Tasks

* Deploy
* Monitor
* Configure
* Update

---

## 2. Efficient Resource Utilization

Kubernetes schedules workloads based on:

* CPU
* Memory
* Available resources

Benefits:

* Prevents over-provisioning
* Prevents under-utilization

---

## 3. Self-Healing

Automatically:

* Detect failures
* Restart containers
* Replace unhealthy containers

---

## 4. Scalability

Supports:

### Manual Scaling

Administrator-controlled

### Automatic Scaling

Metric-driven scaling

Example:

* CPU Usage
* Memory Usage

---

## 5. High Availability

Multi-node architecture provides:

* Failover
* Redundancy
* Fault tolerance

---

## 6. Rolling Updates

Deploy new versions without downtime.

### Benefits

* Zero downtime deployments
* Smooth upgrades

---

## 7. Rollbacks

Instantly revert to a previous stable version.

---

## 8. Service Discovery

Built-in DNS support.

Applications find each other automatically.

---

## 9. Load Balancing

Traffic is automatically distributed among pod replicas.

---

## 10. Security Features

### RBAC

Role-Based Access Control

### ConfigMaps

Store configurations

### Secrets

Store sensitive information securely

### Network Policies

Control network communication

---

## 11. Infrastructure Abstraction

Works consistently on:

* On-Premises
* Cloud
* Hybrid Environments

---

## 12. Declarative Configuration

Uses YAML manifests.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
```

Desired state is defined in code.

---

## 13. CI/CD Integration

Integrates seamlessly with:

* Jenkins
* GitOps
* ArgoCD
* GitLab CI/CD
* Azure DevOps

---

# 8. Kubernetes Architecture

---

## Basic Architecture

```text
                 Control Plane
                 (Master Node)
                       |
        ---------------------------------
        |               |              |
    Worker Node 1   Worker Node 2  Worker Node 3
        |               |              |
       Pods            Pods           Pods

                    ETCD
              (Cluster Database)
```

---

# 9. Main Components

Kubernetes architecture consists of:

### 1. Control Plane (Master Node)

Cluster management layer

### 2. Worker Nodes

Run applications

### 3. ETCD

Stores cluster information

---

# 10. Control Plane (Master Node)

The control plane manages the entire cluster.

### Responsibilities

* Scheduling
* Scaling
* Desired State Management
* Updates
* Cluster Orchestration

---

# Components of Control Plane

---

## 1. kube-apiserver

### Purpose

Front-end of Kubernetes.

All communication passes through it.

### Communicates With

* Users
* kubectl
* APIs
* External tools

---

## 2. ETCD

### Purpose

Distributed Key-Value Store

Stores:

* Cluster State
* Configuration
* Metadata

### Important

> ETCD is the Single Source of Truth for Kubernetes Cluster.

---

## 3. kube-scheduler

### Purpose

Assigns pods to worker nodes.

### Decision Factors

* CPU availability
* Memory availability
* Policies
* Constraints

---

## 4. kube-controller-manager

Runs controllers that ensure:

```text
Desired State
      =
Actual State
```

Examples:

* Node Controller
* Replication Controller

---

## 5. Cloud Controller Manager (Optional)

Used when Kubernetes runs in cloud environments.

Manages:

* Cloud resources
* Load balancers
* Cloud networking

---

# 11. Worker Node

Worker nodes are machines where applications actually run.

Can be:

* Physical Machines
* Virtual Machines

---

# Worker Node Components

---

## 1. Kubelet

Agent running on every worker node.

### Responsibilities

* Communicates with Control Plane
* Ensures pods are running
* Performs health checks

---

## 2. kube-proxy

Networking component.

### Responsibilities

* Routing
* Service Networking
* Load Balancing

Ensures traffic reaches the correct pod.

---

## 3. Container Runtime

Software responsible for running containers.

Examples:

* Docker
* containerd
* CRI-O

### Responsibilities

* Pull images
* Start containers
* Stop containers

---

## 4. Pods

Smallest deployable units in Kubernetes.

Contain:

* One Container
* Multiple Containers

---

# 12. Types of Kubernetes Clusters

Two major categories:

## 1. Self-Managed Kubernetes

## 2. Managed Kubernetes

---

# 13. Self-Managed Kubernetes Cluster

Also called:

### User Managed Cluster

Organization installs and manages everything.

### Responsibilities

* Installation
* Upgrades
* Security
* Networking
* Monitoring
* Scaling

---

## Examples

* On-Prem Kubernetes
* Bare Metal Kubernetes
* VM-Based Kubernetes

---

## Advantages

✅ Full Control
✅ High Flexibility
✅ Lower Long-Term Cost

---

## Disadvantages

❌ High Complexity
❌ Requires Advanced Kubernetes Skills
❌ Risk of Misconfiguration
❌ Must Handle HA, Backup, Recovery

---

# 14. Managed Kubernetes Cluster

Cloud provider manages Kubernetes infrastructure.

Examples include:

* Amazon Web Services EKS (Elastic Kubernetes Service)
* Google Cloud GKE (Google Kubernetes Engine)
* Microsoft Azure AKS (Azure Kubernetes Service)
* IBM Kubernetes Service
* DigitalOcean Kubernetes Service

---

## Cloud Provider Handles

* Control Plane
* Upgrades
* Patching
* Backups
* High Availability

---

## User Handles

* Applications
* Pods
* Worker Nodes (sometimes)

---

## Advantages

✅ Easier Operations
✅ Auto Upgrades
✅ High Availability
✅ Better Security Integration
✅ Faster Setup

---

## Disadvantages

❌ Less Flexibility
❌ Higher Cost
❌ Vendor Lock-in
❌ Limited Control Plane Access

---

# Session 2 Summary

### Kubernetes is:

* Open-source
* Container orchestration platform
* Automates deployment and management

### Major Features

* Self-Healing
* Auto Scaling
* Load Balancing
* Service Discovery
* High Availability
* Rolling Updates
* Rollbacks
* Security Controls

### Architecture

```text
Kubernetes Cluster
│
├── Control Plane
│   ├── API Server
│   ├── Scheduler
│   ├── Controller Manager
│   └── ETCD
│
└── Worker Nodes
    ├── Kubelet
    ├── Kube Proxy
    ├── Container Runtime
    └── Pods
```

# Interview Questions

1. What is Kubernetes and why is it used?
2. What is Container Orchestration?
3. What is a Pod?
4. Difference between Container and Pod?
5. What is ETCD?
6. What is the role of kube-apiserver?
7. What is kubelet?
8. What is kube-proxy?
9. What is the difference between Master Node and Worker Node?
10. What is Self-Healing in Kubernetes?
11. What is Horizontal Pod Autoscaling (HPA)?
12. Difference between Vertical and Horizontal Scaling?
13. What are ConfigMaps and Secrets?
14. What are Rolling Updates and Rollbacks?
15. Difference between Self-Managed and Managed Kubernetes Clusters?
