# ConfigMaps

- ConfigMaps store **non-sensitive** configuration as key-value pairs, like environment variables, config files, or command-line args.
- Can be mounted as volumes or exposed as environment variables inside Pods.
- Ideal for app settings that can be safely seen (e.g., log level, feature flags).


## Helpful commands

### List ConfigMaps
```bash
k get configmap
```

### Create a ConfigMap with variables

```bash
k create configmap db-config --from-literal=ENV=production --from-literal=DB_HOST=sql01.example.com
```

### View a ConfigMap
```bash
k describe configmap db-config
```

### Edit a ConfigMap
```bash
k edit configmap db-config
```

### Configure a Pod to Use a ConfigMap

YAML file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox:1.27.2
      command: [ "/bin/sh", "-c", "env" ]
      envFrom:
      - configMapRef:
          name: my-config
  restartPolicy: Never
```
