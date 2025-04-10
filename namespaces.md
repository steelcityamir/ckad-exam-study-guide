# Namespaces
Namespaces create separate environments within a Kubernetes cluster, preventing resource conflicts.

- Resources must have unique names within a namespace but can share names across namespaces.
- Helps organize workloads for different teams, applications, or environments (e.g., dev, test, prod).
- Allows applying role-based access control (RBAC) and resource limits per namespace.

A `default` namespace always exists.

![image](https://github.com/user-attachments/assets/19c5ca85-5cff-455b-91e2-fe5f19b2e156)

## Helpful commands

### List namespaces
```bash
k get ns
```

### Create a namespace
```bash
k create ns dev-ns
```

### Switch the current namespace
```bash
k config set-context --current --namespace=dev-ns
```
