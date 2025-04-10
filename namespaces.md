# Namespaces
Namespaces create separate environments within a Kubernetes cluster, preventing resource conflicts.

- Resources must have unique names within a namespace but can share names across namespaces.
- Helps organize workloads for different teams, applications, or environments (e.g., dev, test, prod).
- Allows applying role-based access control (RBAC) and resource limits per namespace.

A `default` namespace always exists.

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
