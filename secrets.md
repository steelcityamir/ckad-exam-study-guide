# Secrets

- Store sensitive data like passwords, API keys, and TLS certificates — also as key-value pairs.
- Automatically Base64-encoded, but not encrypted by default (encryption at rest must be enabled separately).
- Use like ConfigMaps (env vars or volume mounts), but with stricter access control via RBAC.

## Helpful commmands

### Create a secret

```bash
kubectl create secret generic api-secret --from-literal=API_KEY=abc123
```

### View a secret in plaintext

#### Get base64-encoded secret from YAML
```bash
kubectl get secret api-secret -o yaml
```
Output:
<pre data-no-copy>
apiVersion: v1
data:
  API_KEY: YWJjMTIz
kind: Secret
metadata:
  creationTimestamp: "2025-04-11T13:31:21Z"
  name: api-secret
  namespace: dev
  resourceVersion: "120699"
  uid: fdb43c3f-6bff-4d50-8c80-42495e77b3f4
type: Opaque
</pre>

#### Decode the secret
```bash
echo -n YWJjMTIz | base64 --decode
```

Output:
<pre data-no-copy>
abc123
</pre>

### Configure Pod YAML to use secret

YAML file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: envars-test-container
    image: nginx
    envFrom:
    - secretRef:
        name: api-secret
```
