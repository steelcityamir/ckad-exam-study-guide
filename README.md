# CKAD Exam Study Guide
Study guide for the [Certified Kubernetes Application Developer (CKAD)](https://training.linuxfoundation.org/certification/certified-kubernetes-application-developer-ckad/) exam.

## Exam Tips
- You have 2 hours to complete 15-20 tasks.
- Don't spend too much time on any one question.  
- Questions can be answered in any order.
- The `--help` switch provides more information on command usage.
- You do have access to K8s documentation.
- When deleting a pod, use `--force` to delete it quicker.
- To save time, create an alias for `kubectl` using `alias k=kubectl`.
- Don’t write YAML file for any questions from scratch. Instead, copy the YAML examples from official docs or use `--dry-run=client -o yaml`.

## Running K8s locally
To practice commands, you can use [minikube](https://minikube.sigs.k8s.io/docs/start/) to run Kubernetes on your machine.

## Contents

- [Namespaces](namespaces.md)
- [Pods](pods.md)
- [ReplicaSets](replicasets.md)
- [Deployments](deployments.md)
- [Services](services.md)
- [ConfigMaps](configmaps.md)
- [Secrets](secrets.md)
- [Taints and Tolerations](taints-tolerations.md)
- [Network Policies](network-policies.md)
  
