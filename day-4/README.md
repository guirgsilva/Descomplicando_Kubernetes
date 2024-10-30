# Kubernetes: Day 4 - Working with ReplicaSets, DaemonSets, and Probes

This document covers essential Kubernetes objects for managing Pods across clusters: **ReplicaSets**, **DaemonSets**, and **Probes**. Today, we’ll explore each of these objects, understand their use cases, and implement examples to solidify these concepts.

---

## Table of Contents
1. [Introduction](#introduction)
2. [ReplicaSets](#replicasets)
   - [Creating a ReplicaSet](#creating-a-replicaset)
   - [Scaling a ReplicaSet](#scaling-a-replicaset)
   - [Updating a ReplicaSet](#updating-a-replicaset)
   - [Deleting a ReplicaSet](#deleting-a-replicaset)
3. [DaemonSets](#daemonsets)
   - [Creating a DaemonSet](#creating-a-daemonset)
   - [Managing a DaemonSet Across Cluster Nodes](#managing-a-daemonset-across-cluster-nodes)
   - [Deleting a DaemonSet](#deleting-a-daemonset)
4. [Kubernetes Probes](#kubernetes-probes)
   - [Liveness Probe](#liveness-probe)
   - [Readiness Probe](#readiness-probe)
   - [Startup Probe](#startup-probe)
   - [Combining Probes](#combining-probes)
5. [Conclusion](#conclusion)

---

## Introduction

In this module, we’ll dive into **ReplicaSets** and **DaemonSets** in Kubernetes and how they are used for managing applications across nodes in a cluster. We’ll also cover **Probes**, which are essential for maintaining application health by ensuring Pods are correctly initialized, ready, and functioning as expected.

---

## ReplicaSets

A **ReplicaSet** is a Kubernetes object that ensures a specified number of replica Pods are running at any given time. When combined with Deployments, ReplicaSets manage Pod scaling and redundancy.

### Creating a ReplicaSet

The following example creates a simple ReplicaSet for NGINX:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx
spec:
  replicas: 3
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
        image: nginx:1.19.2
        resources:
          limits:
            cpu: "0.5"
            memory: "256Mi"
          requests:
            cpu: "0.25"
            memory: "128Mi"
```

Apply the configuration:

```bash
kubectl apply -f nginx-replicaset.yaml
```

### Scaling a ReplicaSet

You can scale a ReplicaSet to increase or decrease the number of Pods:

```bash
kubectl scale replicaset nginx-replicaset --replicas=5
```

### Updating a ReplicaSet

Updating a ReplicaSet’s Pod image requires manual deletion and replacement, as ReplicaSets do not manage versioning:

```yaml
# Edit the image in nginx-replicaset.yaml to a new version
image: nginx:1.19.3
```

Apply the update:

```bash
kubectl apply -f nginx-replicaset.yaml
```

### Deleting a ReplicaSet

To remove a ReplicaSet along with all its Pods:

```bash
kubectl delete -f nginx-replicaset.yaml
```

---

## DaemonSets

A **DaemonSet** ensures that a Pod runs on all (or specific) nodes within a cluster, useful for tasks like logging, monitoring, or networking agents.

### Creating a DaemonSet

The example below creates a DaemonSet for the Prometheus Node Exporter:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter:latest
        ports:
        - containerPort: 9100
          hostPort: 9100
        volumeMounts:
        - name: proc
          mountPath: /host/proc
          readOnly: true
        - name: sys
          mountPath: /host/sys
          readOnly: true
      volumes:
      - name: proc
        hostPath:
          path: /proc
      - name: sys
        hostPath:
          path: /sys
```

Deploy the DaemonSet:

```bash
kubectl apply -f node-exporter-daemonset.yaml
```

### Managing a DaemonSet Across Cluster Nodes

DaemonSets automatically adjust to the number of nodes. If a node is added, a new Pod is created on it. Use the following to scale nodes (assuming you’re using `eksctl` or another tool):

```bash
eksctl scale nodegroup --cluster=my-cluster --nodes=3 --name=nodegroup-name
```

### Deleting a DaemonSet

To remove the DaemonSet:

```bash
kubectl delete daemonset node-exporter
```

---

## Kubernetes Probes

**Probes** allow Kubernetes to check if Pods are running as expected. There are three main types:

- **Liveness Probe**: Checks if the Pod is running. If it fails, Kubernetes restarts the Pod.
- **Readiness Probe**: Checks if the Pod is ready to receive traffic.
- **Startup Probe**: Ensures the application inside the container starts properly.

### Liveness Probe

A liveness probe can use TCP or HTTP to confirm that a container is running. Here’s an example:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3
```

Add to your Deployment file:

```bash
kubectl apply -f nginx-liveness.yaml
```

### Readiness Probe

The readiness probe determines if the container is ready to accept traffic:

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 5
  successThreshold: 1
  failureThreshold: 3
```

### Startup Probe

The startup probe checks if the container has started correctly, running once during initialization:

```yaml
startupProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3
```

### Combining Probes

Here’s a comprehensive example using all three probes in a single Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
        image: nginx:1.19.2
        livenessProbe:
          exec:
            command: ["curl", "-f", "http://localhost:80/"]
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
        startupProbe:
          tcpSocket:
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
```

Apply the configuration:

```bash
kubectl apply -f nginx-all-probes.yaml
```

---

## Conclusion

**Day-4** introduced **ReplicaSets**, **DaemonSets**, and **Probes**—key elements in Kubernetes for maintaining and scaling applications. By implementing these, you can ensure high availability, efficient resource management, and healthy application states within your cluster.
```
