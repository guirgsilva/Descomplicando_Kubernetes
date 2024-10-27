# Kubernetes Pod with Resource Limits, Requests, and Shared Volume

This project explores a Kubernetes configuration with multiple containers in a single Pod. The setup includes:
- Resource limits and requests for each container
- A shared volume between containers using `EmptyDir`

The project demonstrates efficient resource management and data sharing within a Kubernetes Pod.

---

## Pod Configuration

This YAML configuration file defines a Pod named `resource-limited-pod` with two containers:
- **web-server**: Runs an NGINX server
- **utility**: Runs a lightweight Alpine Linux container

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-limited-pod
spec:
  containers:
    - name: web-server
      image: nginx
      ports:
        - containerPort: 80
      resources:
        limits:
          memory: "128Mi"
          cpu: "0.5"  # 50% of a CPU core
        requests:
          memory: "64Mi"
          cpu: "0.3"  # 30% of a CPU core
      volumeMounts:
        - name: shared-storage
          mountPath: /shared-data
    - name: utility
      image: alpine
      args: ["sleep", "3600"]
      resources:
        limits:
          memory: "64Mi"
          cpu: "0.2"
        requests:
          memory: "32Mi"
          cpu: "0.1"
      volumeMounts:
        - name: shared-storage
          mountPath: /shared-data
  volumes:
    - name: shared-storage
      emptyDir:
        sizeLimit: "256Mi"
```

---

## Explanation of Configuration

### Containers and Resource Management

1. **`web-server` Container**:
   - Runs the `nginx` image and exposes port 80.
   - **Limits**:
     - **Memory**: 128 Mi
     - **CPU**: 0.5 cores (50% of a CPU)
   - **Requests**:
     - **Memory**: 64 Mi
     - **CPU**: 0.3 cores (30% of a CPU)

2. **`utility` Container**:
   - Runs the `alpine` image and executes `sleep 3600` to keep the container running.
   - **Limits**:
     - **Memory**: 64 Mi
     - **CPU**: 0.2 cores (20% of a CPU)
   - **Requests**:
     - **Memory**: 32 Mi
     - **CPU**: 0.1 cores (10% of a CPU)

### Shared Volume

The Pod uses an `EmptyDir` volume, which is a temporary storage space shared between the two containers:
- **Name**: `shared-storage`
- **Mount Path**: `/shared-data` in both containers
- **Size Limit**: 256 Mi

The `EmptyDir` volume allows data exchange between the containers while the Pod is running. When the Pod is deleted, the volume and its contents are also removed.

---

## Deployment and Testing

1. **Deploy the Pod**:
   Save the YAML to a file named `pod-complete.yaml` and apply it using `kubectl`:

   ```bash
   kubectl apply -f pod-complete.yaml
   ```

2. **Verify the Pod and Resource Allocation**:
   Use `kubectl describe` to check the resource allocation and volume mounts:

   ```bash
   kubectl describe pod resource-limited-pod
   ```

3. **Access the Shared Volume**:
   - Create a file in the shared directory from the `utility` container:

     ```bash
     kubectl exec -it resource-limited-pod -c utility -- sh
     echo "Shared data" > /shared-data/shared-file.txt
     ```

   - Verify the file from the `web-server` container:

     ```bash
     kubectl exec -it resource-limited-pod -c web-server -- cat /shared-data/shared-file.txt
     ```

   You should see the content `Shared data`, confirming that the volume is shared between containers.

---

## Summary

This setup showcases:
- **Resource Limits and Requests**: Efficient resource management ensures that each container runs within specified CPU and memory constraints.
- **Shared Volume**: An `EmptyDir` volume facilitates data sharing between containers, useful for scenarios where temporary data exchange is required.

---

## Cleanup

To delete the Pod and release resources:

```bash
kubectl delete pod resource-limited-pod
```

---

## Conclusion

This project provides a practical setup for managing container resources and shared storage in a multi-container Pod in Kubernetes. By using resource limits and shared volumes, we can improve workload efficiency and inter-container communication.

