---

# Descomplicando Kubernetes - Expert Mode: Day-1

Welcome to **Day-1** of "Descomplicando Kubernetes - Expert Mode." This guide provides a foundational understanding of Kubernetes, including container basics, setting up clusters, and essential Kubernetes components.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Introduction to Kubernetes](#introduction-to-kubernetes)
3. [Container Engines and Runtimes](#container-engines-and-runtimes)
4. [Installing and Customizing `kubectl`](#installing-and-customizing-kubectl)
5. [Creating a Local Kubernetes Cluster](#creating-a-local-kubernetes-cluster)
6. [Key Kubernetes Concepts](#key-kubernetes-concepts)
7. [Getting Started with `kubectl`](#getting-started-with-kubectl)
8. [Cleaning Up](#cleaning-up)
9. [Additional Resources](#additional-resources)

---

## Prerequisites

To follow along with Day-1 content, you’ll need:
- Basic understanding of containers
- Familiarity with the Linux terminal
- Access to a local or virtualized environment with at least 2GB RAM and a processor with virtualization support (for running Minikube or Kind)

---

## Introduction to Kubernetes

Kubernetes, or "k8s," is a container orchestrator initially developed by Google. It enables efficient deployment, scaling, and management of containerized applications in a clustered environment. Kubernetes was open-sourced to give developers a platform that’s easy to scale, manage, and automate.

### Kubernetes Terminology

- **Pod**: The smallest Kubernetes object, containing one or more containers.
- **Namespace**: A logical isolation unit in the Kubernetes cluster.
- **Deployment**: Ensures a certain number of Pod replicas are running at all times.
- **Service**: Exposes applications within or outside the cluster.

---

## Container Engines and Runtimes

### Container Engine

A **Container Engine** manages containers, images, and volumes, ensuring isolation and resource control. Popular container engines include:
- **Docker**
- **CRI-O**
- **Podman**

### Container Runtime

A **Container Runtime** executes containers on nodes. Runtimes can be:
- **Low-level** (e.g., `runc`, `crun`)
- **High-level** (e.g., `containerd`, `CRI-O`)
- **Sandbox** (e.g., `gVisor`)
- **Virtualized** (e.g., `Kata Containers`)

The **Open Container Initiative (OCI)** defines standards for container runtimes to ensure compatibility across environments.

---

## Installing and Customizing `kubectl`

### Install `kubectl` on Linux

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

### Customize `kubectl`

- **Auto-complete**: Add the following to `~/.bashrc` or `~/.zshrc`:
  ```bash
  source <(kubectl completion bash)
  ```
- **Alias**: Create a shortcut for `kubectl`:
  ```bash
  alias k=kubectl
  complete -F __start_kubectl k
  ```

---

## Creating a Local Kubernetes Cluster

### Minikube

**Minikube** is a local Kubernetes implementation ideal for learning and development. Basic setup:
- **Requirements**: 1 core CPU, 2GB RAM, 20GB disk space
- **Install Minikube**:
  ```bash
  curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  chmod +x ./minikube
  sudo mv ./minikube /usr/local/bin/minikube
  minikube version
  ```

### Kind

**Kind** runs Kubernetes clusters in Docker containers. To install and create a cluster:
- **Install Kind**:
  ```bash
  curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.14.0/kind-linux-amd64
  chmod +x ./kind
  sudo mv ./kind /usr/local/bin/kind
  ```
- **Create a Cluster**:
  ```bash
  kind create cluster
  ```

---

## Key Kubernetes Concepts

- **Pod**: The smallest deployable unit that can contain multiple containers.
- **Namespace**: Provides logical isolation within the cluster.
- **Deployment**: Manages the lifecycle and scaling of Pods.
- **Service**: Exposes Pods to network requests within or outside the cluster.

---

## Getting Started with `kubectl`

### Basic Commands

1. **Check Cluster Nodes**:
   ```bash
   kubectl get nodes
   ```
2. **List Namespaces**:
   ```bash
   kubectl get namespaces
   ```
3. **Create a Pod**:
   ```bash
   kubectl run nginx --image=nginx
   ```
4. **View Pods**:
   ```bash
   kubectl get pods
   ```
5. **Delete a Pod**:
   ```bash
   kubectl delete pod nginx
   ```

### Exposing a Pod

To expose a Pod using a Service:
```bash
kubectl expose pod nginx --port=80 --target-port=80 --type=NodePort
kubectl get services
```

---

## Cleaning Up

Remove resources to keep the cluster tidy:
```bash
kubectl delete pod nginx
kubectl delete service nginx
kubectl delete -f pod-template.yaml
```

---

## Additional Resources

- [Kubernetes Official Docs](https://kubernetes.io)
- [Kubernetes GitHub](https://github.com/kubernetes/kubernetes)
- [CNCF Certifications](https://www.cncf.io/certification/)

---

This Day-1 document serves as an introductory guide to Kubernetes, providing a solid foundation in container orchestration, cluster setup, and basic command usage. Enjoy your journey in mastering Kubernetes!
