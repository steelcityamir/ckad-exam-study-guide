# Network Policies

Network Policies define how groups of pods are allowed to communicate with each other and other network endpoints.

By default, all pods have open access to all other pods in a Kubernetes cluster.

Network Policies are implemented by the network plugin, not Kubernetes itself. Common supporting plugins: Calico, Cilium, Weave Net, Antrea.

> [!NOTE]  
> If your cluster uses a plugin that doesn't support Network Policies (like Flannel), applying policies will have no effect.


## Network Policy Structure

```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: example-policy
  namespace: default
spec:
  podSelector:         # Which pods the policy applies to
    matchLabels:
      role: db
  policyTypes:         # Specifies if the policy has Ingress rules, Egress rules, or both
  - Ingress
  - Egress
  ingress:             # Allowed incoming traffic rules
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 3306
  egress:              # Allowed outgoing traffic rules
  - to:
    - namespaceSelector:
        matchLabels:
          purpose: monitoring
    ports:
    - protocol: TCP
      port: 8080
```

## Key Components
### Pod Selection
Network Policies apply to pods defined by the `podSelector` field. This works with label selectors:

- Empty `podSelector` ({}) selects all pods in the namespace
- Specific labels select only matching pods

## Policy Types
The `policyTypes` field defines whether the policy includes ingress rules, egress rules, or both:

- If only `Ingress` is specified: Only incoming traffic is restricted, outgoing traffic is unrestricted.
- If only `Egress` is specified: Only outgoing traffic is restricted, incoming traffic is unrestricted.
- If both are specified: Both incoming and outgoing traffic are restricted.

## Traffic Selection
Network Policies can control traffic using three selectors:

- `podSelector`: Select pods in the same namespace
- `namespaceSelector`: Select pods in specific namespaces
- `ipBlock`: Select specific IP CIDR ranges

These can be combined in various ways to create precise rules.

## Network Policy Behavior

### Default Rules

No Network Policies selecting a pod: The pod is non-isolated and accepts all traffic
Network Policy selects a pod but provides no rules: All traffic to/from the pod is denied
Multiple Network Policies select a pod: All policies are applied additively (logical OR)

### Rule Evaluation

- Network Policy rules are whitelist-based - they specify what traffic is allowed
- Any traffic not explicitly allowed by a policy is denied
- If multiple policies select the same pod, traffic is allowed if any policy allows it

## Common Network Policy Scenarios
### Default Deny All Ingress Traffic
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: default
spec:
  podSelector: {}  # Selects all pods in the namespace
  policyTypes:
  - Ingress
  # No ingress rules = deny all ingress traffic
```
### Default Deny All Egress Traffic
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
  namespace: default
spec:
  podSelector: {}  # Selects all pods in the namespace
  policyTypes:
  - Egress
  # No egress rules = deny all egress traffic
```
### Allow Traffic Only Between Specific Pods
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-to-db-only
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api
    ports:
    - protocol: TCP
      port: 5432
```
### Allow Traffic from a Different Namespace
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-monitoring
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: monitoring
```
### Allow Traffic from Both Pod and Namespace Selectors
Scenario A: Allow traffic from pods with label app=client AND in namespace with label env=prod (logical AND)
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-prod-clients
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: server
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: client
      namespaceSelector:
        matchLabels:
          env: prod
```
Scenario B: Allow traffic from pods with label app=client OR from any pod in namespace with label env=prod (logical OR)
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-clients-or-prod
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: server
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: client
    - namespaceSelector:
        matchLabels:
          env: prod
```
### Allow External Traffic from Specific IP Block
```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-traffic
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
```
### Allow Egress to Specific External Endpoints

```yaml
yamlapiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-egress-to-specific-endpoints
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

## Common Exam Scenarios
### Restricting Pod Access
You may be asked to configure a policy where:

- Only specific pods can communicate with a database pod
- Only specific protocols and ports are allowed
- External traffic is completely blocked

### Multi-tier Application Security
Configure network policies for a typical 3-tier application:

- Frontend can only communicate with the API tier
- API tier can only communicate with the database tier
- No direct communication from frontend to database

### Namespace Isolation
Implement policies to:

- Allow pods in the same namespace to communicate freely
- Restrict communication between namespaces
- Allow specific cross-namespace communication

## Troubleshooting Tips

Check network plugin: Ensure your cluster's network plugin supports Network Policies
Validation: Use kubectl describe networkpolicy <name> to check policy details
Testing communication: Use temporary pods with tools like curl, nc, or wget to test connectivity
Common issues:

Forgetting to add proper labels to pods
Not specifying both ingress and egress in policyTypes when needed
Misconfiguring pod or namespace selectors

