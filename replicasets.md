# ReplicaSets

- A ReplicaSet ensures a specified number of identical pods are running.
- If a pod crashes, the ReplicaSet will replace it.
- Example: If you specify 3 replicas, K8s ensures there are always 3 pods running.

For the CKAD exam, you probably won’t deal directly with ReplicaSets very often, because they’re typically managed indirectly through Deployments. 
