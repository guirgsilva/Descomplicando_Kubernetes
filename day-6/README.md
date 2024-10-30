# Kubernetes Volumes Explained: ConfigMaps, Persistent Volumes (PV), and Persistent Volume Claims (PVC)

Welcome to the guide on understanding and configuring Kubernetes volumes! Today, we’ll demystify Kubernetes volumes with hands-on examples using various clusters, including **EKS**, **kind**, and cloud provider instances.

---

## Table of Contents
1. [Introduction to Volumes](#introduction-to-volumes)
2. [Types of Volumes in Kubernetes](#types-of-volumes-in-kubernetes)
3. [Working with `emptyDir` Volumes](#working-with-emptydir-volumes)
4. [Storage Classes in Kubernetes](#storage-classes-in-kubernetes)
5. [Persistent Volumes (PV)](#persistent-volumes-pv)
6. [Persistent Volume Claims (PVC)](#persistent-volume-claims-pvc)
7. [Practical Exercise](#practical-exercise)

---

## Introduction to Volumes

In Kubernetes, volumes are directories inside a Pod that can be used to store data. They can be ephemeral or persistent, depending on the application's needs. Volumes are especially important for maintaining data consistency and availability across container restarts.

## Types of Volumes in Kubernetes

Kubernetes supports various volume types:
- **Ephemeral Volumes**: Temporary volumes (e.g., `emptyDir`) created and destroyed with the Pod.
- **Persistent Volumes**: Durable volumes (e.g., PV and PVC) that keep data intact even when the Pod is deleted.

## Working with `emptyDir` Volumes

### What is `emptyDir`?

The `emptyDir` volume is created when a Pod starts and deleted when the Pod is removed. It is commonly used for temporary storage or to share data between containers within the same Pod.

### Configuration: `pod-emptydir.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: giropops
spec:
  containers:
  - name: girus
    image: ubuntu
    args: ["sleep", "infinity"]
    volumeMounts:
    - name: primeiro-emptydir
      mountPath: /giropops
  volumes:
  - name: primeiro-emptydir
    emptyDir:
      sizeLimit: 256Mi
```

This configuration defines an `emptyDir` volume with a size limit of 256Mi. The volume will be mounted at `/giropops` inside the container.

To apply this configuration:
```bash
kubectl apply -f pod-emptydir.yaml
```

## Storage Classes in Kubernetes

A **StorageClass** provides a way to define storage policies within a Kubernetes cluster. StorageClasses enable dynamic provisioning of PersistentVolumes (PV) based on user-defined requirements. For instance, cloud providers like AWS and Azure have provisioners such as `kubernetes.io/aws-ebs` and `kubernetes.io/azure-disk`.

To list available StorageClasses:
```bash
kubectl get storageclass
```

### Example StorageClass Configuration

Here’s an example of creating a `StorageClass` in a `kind` cluster:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: giropops
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

## Persistent Volumes (PV)

Persistent Volumes are cluster-level storage resources that applications can request through Persistent Volume Claims (PVCs). PVs are durable and ensure data persistence, crucial for applications like databases.

### Example: `pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: meu-pv
  labels:
    storage: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
  storageClassName: standard
```

To create the PV:
```bash
kubectl apply -f pv.yaml
```

## Persistent Volume Claims (PVC)

PVCs are requests for storage by applications. They allow users to claim storage of a specified size and access mode without needing to understand the details of the storage.

### Example: `pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meu-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: nfs
  selector:
    matchLabels:
      storage: nfs
```

To create the PVC:
```bash
kubectl apply -f pvc.yaml
```

## Practical Exercise

### Task: Create a Nginx Deployment with a Mounted Volume

1. Define a `Deployment` for Nginx.
2. Use a Persistent Volume mounted on `/usr/share/nginx/html` to persist content.
3. Test with different provisioners or PV types as per your needs.

---

By following this guide, you now have hands-on experience with `emptyDir`, Persistent Volumes, PVCs, and StorageClasses in Kubernetes. Continue experimenting to deepen your understanding!
```
