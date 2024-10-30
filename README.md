# Kubernetes Resources Overview

This repository contains a structured guide for various Kubernetes objects and concepts, organized by different learning modules (Day 2, Day 3, etc.). Each section explores a critical Kubernetes component with practical examples, YAML configurations, and essential commands to manage resources in Kubernetes effectively.

---

## Table of Contents

- [Day 2 - Pod with Resource Limits, Requests, and Shared Volume](#day-2---pod-with-resource-limits-requests-and-shared-volume)
- [Day 3 - Understanding and Managing Deployments](#day-3---understanding-and-managing-deployments)
- [Day 4 - Working with ReplicaSets, DaemonSets, and Probes](#day-4---working-with-replicasets-daemonsets-and-probes)
- [Day 6 - Volumes Explained: ConfigMaps, Persistent Volumes (PV), and Persistent Volume Claims (PVC)](#day-6---volumes-explained-configmaps-persistent-volumes-pv-and-persistent-volume-claims-pvc)

---

### Day 2 - Pod with Resource Limits, Requests, and Shared Volume

**Overview**: In Day 2, we focus on **Pods** and understand resource management and shared storage within a Pod.

#### Key Concepts

- **Resource Limits and Requests**: Defines the minimum and maximum CPU and memory resources for each container.
- **Shared Volume**: Enables data sharing between containers within the same Pod.

#### Essential Commands

1. **Create a Pod with resource limits and requests**:
   ```bash
   kubectl apply -f pod-resource-limits.yaml
   ```
   - This command applies the configuration to create a Pod with specified resource limits and requests.

2. **Check Pod resource usage**:
   ```bash
   kubectl describe pod <pod-name>
   ```
   - Provides detailed information about the Pod, including its resource limits and current usage.

3. **Get all running Pods**:
   ```bash
   kubectl get pods
   ```
   - Lists all Pods, showing their status and age.

4. **Delete a Pod**:
   ```bash
   kubectl delete pod <pod-name>
   ```
   - Deletes the specified Pod from the cluster.

---

### Day 3 - Understanding and Managing Deployments

**Overview**: Day 3 introduces **Deployments**, which automate managing, scaling, and updating applications in Kubernetes.

#### Key Concepts

- **Creating a Deployment**: Defines a desired state for Pods and manages the deployment process.
- **Updating Deployments**: Uses strategies like **RollingUpdate** and **Recreate**.
- **Rollbacks**: Reverts to a previous stable state when needed.

#### Essential Commands

1. **Create or update a Deployment**:
   ```bash
   kubectl apply -f deployment.yaml
   ```
   - Creates or updates a Deployment using a configuration file.

2. **Scale a Deployment**:
   ```bash
   kubectl scale deployment <deployment-name> --replicas=3
   ```
   - Changes the number of Pod replicas for a Deployment to match the specified count.

3. **Monitor Deployment rollout status**:
   ```bash
   kubectl rollout status deployment <deployment-name>
   ```
   - Shows the status of the Deployment rollout, useful for monitoring ongoing updates.

4. **Undo a Deployment to the previous revision**:
   ```bash
   kubectl rollout undo deployment <deployment-name>
   ```
   - Rolls back the Deployment to its previous configuration if an update fails.

5. **View Deployment details**:
   ```bash
   kubectl describe deployment <deployment-name>
   ```
   - Provides detailed information about the Deployment, including the ReplicaSets it manages.

---

### Day 4 - Working with ReplicaSets, DaemonSets, and Probes

**Overview**: Day 4 covers **ReplicaSets**, **DaemonSets**, and **Probes**, crucial for cluster reliability and application health.

#### Key Concepts

- **ReplicaSets**: Ensures a specified number of replicas are running, typically managed by Deployments.
- **DaemonSets**: Runs a Pod on all or specified nodes in a cluster.
- **Probes**: Health checks to determine if Pods are running and ready to serve traffic.

#### Essential Commands

1. **Create a ReplicaSet**:
   ```bash
   kubectl apply -f replicaset.yaml
   ```
   - Deploys a ReplicaSet configuration to manage a set number of replicas for a Pod.

2. **Scale a ReplicaSet**:
   ```bash
   kubectl scale replicaset <replicaset-name> --replicas=3
   ```
   - Changes the number of replicas managed by the ReplicaSet.

3. **Create a DaemonSet**:
   ```bash
   kubectl apply -f daemonset.yaml
   ```
   - Deploys a DaemonSet to run a Pod on each node in the cluster.

4. **Get DaemonSet details**:
   ```bash
   kubectl describe daemonset <daemonset-name>
   ```
   - Shows detailed information about the DaemonSet and the nodes it’s running on.

5. **Check Pod health with Probes**:
   ```bash
   kubectl describe pod <pod-name>
   ```
   - Displays the status of Probes, including **Liveness**, **Readiness**, and **Startup** Probes, for a Pod.

6. **Delete a DaemonSet**:
   ```bash
   kubectl delete daemonset <daemonset-name>
   ```
   - Removes the DaemonSet and all Pods it manages from the cluster.

---

### Day 6 - Volumes Explained: ConfigMaps, Persistent Volumes (PV), and Persistent Volume Claims (PVC)

**Overview**: Day 6 explores **Volumes** in Kubernetes, focusing on **ConfigMaps**, **Persistent Volumes (PV)**, and **Persistent Volume Claims (PVC)** for persistent storage and dynamic configuration.

#### Key Concepts

- **ConfigMaps**: Stores configuration data outside of container images, allowing for easy configuration changes.
- **Persistent Volumes (PV)**: Cluster-level storage resources, separate from Pods.
- **Persistent Volume Claims (PVC)**: Requests for PVs, enabling Pods to use persistent storage.

#### Essential Commands

1. **Create a ConfigMap**:
   ```bash
   kubectl create configmap <configmap-name> --from-literal=<key>=<value>
   ```
   - Creates a ConfigMap directly from the command line.

2. **Get ConfigMap details**:
   ```bash
   kubectl get configmap <configmap-name> -o yaml
   ```
   - Displays the contents of the ConfigMap in YAML format.

3. **Create a Persistent Volume (PV)**:
   ```bash
   kubectl apply -f persistent-volume.yaml
   ```
   - Deploys a Persistent Volume to the cluster, specifying storage capacity and access modes.

4. **Create a Persistent Volume Claim (PVC)**:
   ```bash
   kubectl apply -f persistent-volume-claim.yaml
   ```
   - Requests storage from a PV for use by Pods.

5. **View Persistent Volumes and Claims**:
   ```bash
   kubectl get pv
   kubectl get pvc
   ```
   - Lists all Persistent Volumes and Persistent Volume Claims, showing their status and capacity.

6. **Delete a Persistent Volume or PVC**:
   ```bash
   kubectl delete -f persistent-volume.yaml
   kubectl delete pvc <pvc-name>
   ```
   - Deletes specified Persistent Volumes and/or Claims.

---

## Summary

This repository provides a comprehensive overview of Kubernetes resources and best practices, with each module building foundational skills to manage complex applications in Kubernetes. From Pods and resource management to Deployments, ReplicaSets, and Volumes, this guide will help you gain practical insights into Kubernetes operations.
```
