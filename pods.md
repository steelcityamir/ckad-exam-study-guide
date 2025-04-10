# Pods

Pods are the smallest deployable unit in Kubernetes.

A pod can have one or more containers that share storage and networking.

Pods are ephemeral — if a pod dies, it's gone unless managed by a controller.

![image](https://github.com/user-attachments/assets/5377a21d-5611-47f3-951b-b5fc5b325b33)

## Helpful commands

### List pods in current namespace
```bash
kubectl get pod
```

### List pods in marketing namespace
```bash
kubectl get pod -n=dev
```

### List pods in all namespaces
```bash
kubectl get pod --all-namespaces
```

### Create a pod named redis running the redis image
```bash
kubectl run redis --image=redis
```

### View details of the redis pod
```bash
kubectl describe pod redis
```

### Delete a pod
```bash
kubectl delete pod redis --force
```

### Generate a pod definition yaml file for redis
```bash
kubectl run redis --image=redis --dry-run=client -o yaml > redis.yaml
```

### Generate a pod definition yaml file from a running pod
```bash
kubectl get pod webapp-color -o yaml > pod.yaml
```

### Create a pod from YAML
```bash
k create -f pod.yaml
```
