# Kubernetes: Day 3 - Understanding and Managing Deployments

Today’s content will focus on the **Deployment** object in Kubernetes. We’ll cover essential concepts such as creating, updating, and managing Deployments, including update strategies, rollbacks, and more.

---

## Table of Contents
1. [Introduction to Deployments](#introduction-to-deployments)
2. [Creating a Deployment](#creating-a-deployment)
3. [Understanding the Deployment YAML File](#understanding-the-deployment-yaml-file)
4. [Applying the Deployment](#applying-the-deployment)
5. [Verifying the Deployment and Pods](#verifying-the-deployment-and-pods)
6. [Deployment Update Strategies](#deployment-update-strategies)
7. [Rolling Update Strategy](#rolling-update-strategy)
8. [Recreate Strategy](#recreate-strategy)
9. [Rollback and Deployment History](#rollback-and-deployment-history)
10. [Removing a Deployment](#removing-a-deployment)
11. [Conclusion](#conclusion)

---

## Introduction to Deployments

In Kubernetes, a **Deployment** is an object that manages a set of replicated Pods. It provides features for updating and rolling back applications, ensuring the specified number of replicas are running, and automatically replacing any failed Pods.

### Key Features
- Ensures specified replica count
- Provides declarative updates and rollbacks
- Manages ReplicaSets, which manage Pods

---

## Creating a Deployment

To create a Deployment, start by writing a YAML configuration file named `deployment.yaml`.

### Example Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  strategy: {}
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx
        name: nginx
        resources:
          limits:
            cpu: "0.5"
            memory: 256Mi
          requests:
            cpu: "0.25"
            memory: 128Mi
```

---

## Understanding the Deployment YAML File

Each section of the YAML file has a specific function:

- **apiVersion**: Specifies the API version (`apps/v1`) used to manage the Deployment.
- **kind**: Defines the object type as `Deployment`.
- **metadata**: Includes labels and a name to identify the Deployment.
- **spec**:
  - **replicas**: Number of desired Pods.
  - **selector**: Labels used to match Pods managed by the Deployment.
  - **strategy**: Specifies the update strategy (default is `RollingUpdate`).
  - **template**: Template for creating Pods.

---

## Applying the Deployment

To deploy the application using the YAML file, run:

```bash
kubectl apply -f deployment.yaml
```

---

## Verifying the Deployment and Pods

### Check Deployment Status

```bash
kubectl get deployments -l app=nginx-deployment
```

### Check Managed Pods

```bash
kubectl get pods -l app=nginx-deployment
```

### Check ReplicaSet Managed by the Deployment

```bash
kubectl get replicasets -l app=nginx-deployment
```

### View Deployment Details

```bash
kubectl describe deployment nginx-deployment
```

---

## Deployment Update Strategies

Kubernetes provides two primary update strategies for Deployments:
- **RollingUpdate** (default): Updates Pods gradually to avoid downtime.
- **Recreate**: Removes all Pods and then recreates them with the new specification.

### Applying Updates
Edit `deployment.yaml` to specify a different image version or resource limits, and apply the changes with:

```bash
kubectl apply -f deployment.yaml
```

---

## Rolling Update Strategy

To specify the `RollingUpdate` strategy, modify the `deployment.yaml` as follows:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 2
```

#### Explanation
- **maxSurge**: Allows an extra Pod to be created during updates.
- **maxUnavailable**: Allows up to 2 Pods to be temporarily unavailable during updates.

To monitor rollout progress:

```bash
kubectl rollout status deployment nginx-deployment
```

---

## Recreate Strategy

With the `Recreate` strategy, all existing Pods are removed before new Pods are created.

```yaml
strategy:
  type: Recreate
```

This strategy ensures consistency by preventing multiple versions from running simultaneously but may cause temporary downtime.

---

## Rollback and Deployment History

### Rolling Back to a Previous Version

Use the `rollout undo` command to revert to the previous configuration:

```bash
kubectl rollout undo deployment nginx-deployment
```

### Viewing Deployment History

To see a record of Deployment revisions:

```bash
kubectl rollout history deployment nginx-deployment
```

#### Rollback to a Specific Revision

```bash
kubectl rollout undo deployment nginx-deployment --to-revision=1
```

---

## Removing a Deployment

To delete a Deployment:

```bash
kubectl delete deployment nginx-deployment
```

Alternatively, if using the YAML manifest:

```bash
kubectl delete -f deployment.yaml
```

---

## Conclusion

In **Day-3**, we covered the basics and advanced usage of Kubernetes Deployments, including the creation, update strategies, and rollbacks. Deployments are fundamental to managing applications on Kubernetes, and understanding them provides a robust foundation for more complex configurations.
