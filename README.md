# Kubernetes Zero To Hero

A practical Kubernetes learning repository focused on real-world workflows, clear architecture notes, and repeatable hands-on labs.

## Learning Objectives

By the end of this course, you should be able to:

- Explain Kubernetes architecture and core components.
- Deploy workloads using YAML manifests.
- Expose applications with Kubernetes Services.
- Manage configuration using ConfigMaps and Secrets.
- Add health checks, resource requests, and limits.
- Debug common pod and cluster issues.
- Package Kubernetes workloads with Helm.

## Repository Structure

```text
.
|-- README.md
|-- kubernetes-zero-to-hero-7-days.md
|-- day1/
|   |-- README.md
|   |-- nginx-pod.yaml
```

## 7-Day Roadmap

| Day | Focus Area | Practical Outcome |
| --- | --- | --- |
| Day 1 | Kubernetes architecture, Minikube setup, namespaces, pods | Run the first nginx pod from YAML |
| Day 2 | YAML structure, labels, selectors, ReplicaSets, Deployments | Deploy and scale nginx with a Deployment |
| Day 3 | Services and networking | Expose an application with ClusterIP, NodePort, and port-forwarding |
| Day 4 | ConfigMaps, Secrets, and storage | Inject configuration and sensitive data into workloads |
| Day 5 | Probes, resources, and debugging | Add health checks and troubleshoot common failures |
| Day 6 | Ingress, autoscaling, DaemonSets, and RBAC | Understand production-oriented Kubernetes features |
| Day 7 | Helm and final project | Package and deploy a complete Kubernetes application |

## Current Lab Status

Day 1 has been completed locally with Minikube.

Validated environment:

```text
Docker CLI: 29.5.2
kubectl client: v1.34.1
Minikube: v1.36.0
Kubernetes cluster: v1.33.1
Node: minikube Ready
```

Validated workload:

```text
Namespace: day1
Manifest: day1/nginx-pod.yaml
Pod: nginx-pod
Status: Running
Image: nginx:1.27
Container check: nginx/1.27.5
```

## Quick Start

Start the local Kubernetes cluster:

```powershell
minikube start --driver=docker
```

Verify cluster access:

```powershell
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

Run the Day 1 pod lab:

```powershell
kubectl create namespace day1
kubectl apply -f day1/nginx-pod.yaml
kubectl get pods -n day1 -o wide
kubectl describe pod nginx-pod -n day1
kubectl logs nginx-pod -n day1
kubectl exec nginx-pod -n day1 -- nginx -v
```

Clean up the Day 1 lab:

```powershell
kubectl delete -f day1/nginx-pod.yaml
kubectl delete namespace day1
```

## Detailed Notes

- [Day 1: Kubernetes Basics, Architecture, Setup, Namespace, and Pod](day1/README.md)
- [Full 7-Day Roadmap](kubernetes-zero-to-hero-7-days.md)
