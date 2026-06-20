# Session 6 Notes: Mastering Kubernetes Deployment – Imperative vs Declarative

---

# 1. Introduction to Kubernetes Deployment

Kubernetes is a powerful **Container Orchestration Platform** that helps manage, scale, and deploy containerized applications efficiently.

In this session, two major deployment approaches were discussed:

1. **Imperative Deployment**
2. **Declarative Deployment**

Both methods are used to deploy applications on a Kubernetes cluster but serve different purposes.

---

# 2. Kubernetes Deployment Methods

## A. Imperative Method

In the Imperative approach, administrators use **kubectl commands directly** to create and manage resources.

### Characteristics

* Command-based deployment
* Fast and simple
* Suitable for learning and testing
* Good for one-time tasks
* Difficult to reproduce consistently
* Not ideal for CI/CD pipelines

### Example

```bash
kubectl create deployment myapp --image=nginx
```

---

## B. Declarative Method

In the Declarative approach, resources are defined inside YAML files and applied to Kubernetes.

### Characteristics

* YAML-based deployment
* Version controllable
* Reproducible
* Ideal for Production environments
* Supports Infrastructure as Code (IaC)
* Best suited for CI/CD

### Example

```bash
kubectl apply -f deployment.yaml
```

---

# 3. Imperative vs Declarative Comparison

| Feature                | Imperative      | Declarative     |
| ---------------------- | --------------- | --------------- |
| Method                 | Commands        | YAML Files      |
| Ease of Use            | Easy            | Moderate        |
| Speed                  | Fast            | Slightly slower |
| Repeatability          | Poor            | Excellent       |
| Version Control        | Not Available   | Available       |
| CI/CD Friendly         | No              | Yes             |
| Team Collaboration     | Difficult       | Easy            |
| Production Usage       | Not Recommended | Recommended     |
| Infrastructure as Code | No              | Yes             |

---

# 4. Kubernetes Deployment Prerequisites

Before deploying applications, ensure:

### 1. Kubernetes Cluster

A running cluster is required.

Examples:

* Minikube
* kubeadm Cluster
* Docker Desktop Kubernetes
* Cloud Kubernetes Clusters

### 2. kubectl Installed

Verify:

```bash
which kubectl
```

Expected output:

```bash
/usr/bin/kubectl
```

### 3. Basic Container Knowledge

You should understand:

* Docker Images
* Containers
* Registries
* Docker Hub

### 4. Application Image

A container image must be available.

Example:

```bash
nginx
```

or

```bash
myrepo/myimage
```

---

# 5. Kubernetes Cluster Verification

## Check Node Connectivity

Verify worker nodes are reachable:

```bash
ping worker-node-ip
```

---

## Check kubectl Availability

```bash
which kubectl
```

---

## List Cluster Nodes

```bash
kubectl get nodes
```

Output Example:

```bash
NAME       STATUS   ROLES
master     Ready    control-plane
worker1    Ready    <none>
worker2    Ready    <none>
```

---

# 6. Labeling Worker Nodes

Node labels help identify node roles.

### Syntax

```bash
kubectl label node <node-name> node-role.kubernetes.io/<role>=<role>
```

### Example

```bash
kubectl label node worker1 node-role.kubernetes.io/worker1=worker1

kubectl label node worker2 node-role.kubernetes.io/worker2=worker2
```

Verify:

```bash
kubectl get nodes
```

---

# 7. Imperative Deployment Demo

## Step 1: Create Deployment

Syntax:

```bash
kubectl create deployment <deployment-name> --image=<image-name>
```

Example:

```bash
kubectl create deployment nehra-classes-deployment \
--image=nehraclasses/apache
```

Output:

```bash
deployment.apps/nehraclasses-deployment created
```

---

## Step 2: Verify Deployment

```bash
kubectl get deployments
```

Example:

```bash
NAME                        READY
nehraclasses-deployment     1/1
```

---

## Step 3: Expose Deployment

Create a service using NodePort.

```bash
kubectl expose deployment nehraclasses-deployment \
--type=NodePort \
--port=80
```

---

## Step 4: Verify Service

```bash
kubectl get svc
```

Example:

```bash
NAME                        TYPE       PORT(S)
nehraclasses-deployment     NodePort   80:31830/TCP
```

---

# 8. Scaling Deployments

Scaling increases the number of pod replicas.

## Scale Command

```bash
kubectl scale deployment nehraclasses-deployment \
--replicas=2
```

---

## Verify Scaling

```bash
kubectl get deployments
```

Output:

```bash
READY
2/2
```

---

# 9. Understanding Replicas

## What is a Replica?

A replica is a copy of a pod running the same application.

Example:

```text
Replica 1 → Application Instance
Replica 2 → Application Instance
Replica 3 → Application Instance
```

---

## Why Replicas?

### High Availability

If one pod fails, others continue serving traffic.

### Reliability

Applications remain accessible.

### Scalability

Traffic can be distributed across multiple pods.

---

# 10. Monitoring Kubernetes Resources

## List Deployments

```bash
kubectl get deployments
```

---

## List Pods

```bash
kubectl get pods
```

Shows:

* Pod Name
* Status
* Restart Count
* Age

---

## Detailed Pod Information

```bash
kubectl describe pod <pod-name>
```

Provides:

* Namespace
* Node
* Labels
* IP Address
* Events
* Container Details

---

## View Pod Logs

```bash
kubectl logs <pod-name>
```

Useful for troubleshooting container issues.

---

## List Services

```bash
kubectl get svc
```

---

# 11. Working with Namespaces

## List Namespaces

```bash
kubectl get ns
```

Common namespaces:

| Namespace       | Purpose                    |
| --------------- | -------------------------- |
| default         | User applications          |
| kube-system     | Kubernetes system services |
| kube-public     | Public resources           |
| kube-node-lease | Node heartbeats            |

---

## List Pods Across All Namespaces

```bash
kubectl get pods -A
```

or

```bash
kubectl get pods --all-namespaces
```

---

# 12. Wide Output Format

## Pods with Additional Details

```bash
kubectl get pods -o wide
```

Displays:

* Pod IP
* Node Name
* Internal Information

---

## Nodes with Additional Details

```bash
kubectl get nodes -o wide
```

Displays:

* Internal IP
* OS Image
* Kernel Version
* Container Runtime

---

# 13. Accessing Applications

First identify NodePort:

```bash
kubectl get svc
```

Example:

```bash
80:31830/TCP
```

### Access Format

```text
http://<WorkerNodeIP>:<NodePort>
```

Example:

```text
http://192.168.229.139:31830
```

or

```text
http://192.168.229.141:31830
```

---

# 14. Cleaning Up Imperative Deployment

## Delete Service

```bash
kubectl delete service nehraclasses-deployment
```

---

## Delete Deployment

```bash
kubectl delete deployment nehraclasses-deployment
```

---

## Verify Cleanup

```bash
kubectl get deployments

kubectl get pods

kubectl get svc
```

Expected:

```text
No resources found
```

---

# 15. Advantages and Disadvantages of Imperative Deployment

## Advantages

### Fast

Resources are created immediately.

### Easy

Simple commands.

### Useful for Testing

Ideal for:

* Labs
* Debugging
* Learning

---

## Disadvantages

### Hard to Reproduce

Commands must be remembered.

### No Version Control

Changes cannot be tracked.

### Poor Team Collaboration

Not suitable for shared environments.

### Not Ideal for CI/CD

Automation becomes difficult.

---

# 16. Declarative Deployment Demo

Declarative deployment uses YAML configuration files.

Two files were created:

1. Deployment YAML
2. Service YAML

---

# 17. Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

---

# 18. Understanding Deployment YAML

## apiVersion

Defines Kubernetes API version.

```yaml
apiVersion: apps/v1
```

---

## kind

Resource type.

```yaml
kind: Deployment
```

---

## metadata

Stores resource information.

```yaml
metadata:
  name: nginx-deployment
```

---

## spec

Defines desired state.

Contains:

* Replicas
* Containers
* Images
* Labels

---

## replicas

```yaml
replicas: 2
```

Creates two pod copies.

---

## image

```yaml
image: nginx:latest
```

Defines container image.

---

# 19. Service YAML Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

  type: NodePort
```

---

# 20. Understanding Service YAML

## apiVersion

```yaml
apiVersion: v1
```

---

## kind

```yaml
kind: Service
```

---

## selector

Connects service to matching pods.

```yaml
selector:
  app: nginx
```

---

## ports

```yaml
port: 80
targetPort: 80
```

---

## type

```yaml
type: NodePort
```

Exposes service externally.

NodePort Range:

```text
30000 - 32767
```

---

# 21. Apply YAML Files

Deploy resources:

```bash
kubectl apply -f deployment.yaml

kubectl apply -f service.yaml
```

---

# 22. Verify Declarative Deployment

## Deployments

```bash
kubectl get deployments
```

---

## Pods

```bash
kubectl get pods
```

---

## Services

```bash
kubectl get svc
```

Example:

```bash
nginx-service NodePort 80:31556/TCP
```

---

# 23. Access Declarative Deployment

```text
http://<worker-node-ip>:31556
```

Expected Output:

```text
Welcome to nginx!
```

---

# 24. Rolling Updates in Declarative Deployment

Suppose current image:

```yaml
image: nginx:latest
```

Change to:

```yaml
image: nginx:1.25.2
```

Apply changes:

```bash
kubectl apply -f deployment.yaml
```

---

## What Happens?

Kubernetes performs a:

### Rolling Update

Process:

```text
Old Pod Running
      ↓
New Pod Created
      ↓
New Pod Ready
      ↓
Old Pod Removed
```

Benefits:

* Zero Downtime
* Continuous Availability
* Safe Updates

---

# 25. Generate YAML from Imperative Commands

Useful when moving from Imperative to Declarative approach.

## Command

```bash
kubectl create deployment nginx-demo \
--image=nginx \
--dry-run=client \
-o yaml > deployment.yaml
```

### Explanation

| Option            | Purpose                |
| ----------------- | ---------------------- |
| --dry-run=client  | Do not create resource |
| -o yaml           | Output YAML            |
| > deployment.yaml | Save to file           |

This automatically generates a valid deployment YAML file.

---

# 26. Delete Declarative Resources

Delete using YAML:

```bash
kubectl delete -f service.yaml

kubectl delete -f deployment.yaml
```

---

# 27. Best Practices

## Use Declarative Deployments for Production

Benefits:

* Version Controlled
* Repeatable
* Maintainable

---

## Store YAML Files in Git

Benefits:

* Change Tracking
* Rollback Support
* Collaboration

---

## Prefer apply Instead of create

```bash
kubectl apply -f deployment.yaml
```

Reason:

* Idempotent
* Safer updates
* Better automation

---

## Use GitOps Tools

Popular tools:

* Argo CD
* Flux

---

# Exam & Interview Quick Revision

### Imperative Deployment

```bash
kubectl create deployment
kubectl expose deployment
kubectl scale deployment
kubectl delete deployment
```

### Declarative Deployment

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

### NodePort Range

```text
30000 - 32767
```

### Preferred for Production

✅ Declarative Method

### Preferred for Testing & Debugging

✅ Imperative Method

### Rolling Updates Supported Best Through

✅ Declarative Deployments

### Infrastructure as Code (IaC)

✅ Declarative Approach

---

# Session Summary

This session covered:

* Kubernetes Deployment Fundamentals
* Imperative Deployment using kubectl commands
* Declarative Deployment using YAML files
* Services and NodePort Exposure
* Scaling Applications with Replicas
* Namespaces, Pods, Services, and Nodes
* Rolling Updates
* YAML Structure and Components
* Cleanup Procedures
* Production Best Practices
* Imperative vs Declarative Comparison

**Key Takeaway:**
Use **Imperative Deployment** for learning, testing, and debugging. Use **Declarative Deployment (YAML-based)** for production environments, CI/CD pipelines, GitOps workflows, and team collaboration.
