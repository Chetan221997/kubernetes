# Kubernetes Zero To Hero

A practical Kubernetes learning repository focused on real-world workflows, clear architecture notes, and repeatable hands-on labs.

## Learning Objectives

By the end of this course, you should be able to:

- Explain Kubernetes architecture and core components.
- Deploy workloads using YAML manifests.
- Use labels and selectors to group and filter resources.
- Expose applications with Kubernetes Services and Ingress.
- Manage replicas using ReplicaSets and Deployments.
- Configure Horizontal Pod Autoscaling with Metrics Server.
- Add liveness and readiness probes.
- Package Kubernetes applications with Helm.
- Apply basic RBAC permissions using Roles and RoleBindings.
- Explain Kubernetes Pod, Service, DNS, CNI, ingress, and egress networking.
- Manage configuration using ConfigMaps and Secrets.
- Use basic Kubernetes storage with volumes and PersistentVolumeClaims.
- Debug common pod and cluster issues.

## Repository Structure

```text
.
|-- README.md
|-- kubernetes-zero-to-hero-7-days.md
|-- day1/
|   |-- README.md
|   |-- nginx-pod.yaml
|-- day2/
|   |-- README.md
|   |-- nginx-deployment.yaml
|-- day3/
|   |-- README.md
|   |-- web-deployment.yaml
|   |-- clusterip-service.yaml
|   |-- nodeport-service.yaml
|-- day4/
|   |-- README.md
|   |-- labeled-pods.yaml
|   |-- ecommerce-frontend-service.yaml
|   |-- nginx-replicaset.yaml
|   |-- php-apache-deployment.yaml
|   |-- php-apache-service.yaml
|   |-- php-apache-hpa.yaml
|   |-- RBAC manifests
|-- day5/
|   |-- README.md
|   |-- manifests/
|   |-- helm/day5-ecommerce/
|-- day6/
|   |-- README.md
|   |-- manifests/
|-- day7/
|   |-- README.md
|   |-- manifests/
|   |-- debug/
```

## 7-Day Roadmap

| Day | Focus Area | Practical Outcome |
| --- | --- | --- |
| Day 1 | Kubernetes architecture, Minikube setup, namespaces, pods | Run the first nginx pod from YAML |
| Day 2 | YAML structure, labels, selectors, ReplicaSets, Deployments | Deploy and scale nginx with a Deployment |
| Day 3 | Services and networking | Expose an application with ClusterIP, NodePort, and port-forwarding |
| Day 4 | Labels, selectors, ReplicaSet, HPA, Metrics Server, and RBAC | Test selector filtering, ReplicaSet self-healing, autoscaling, and RBAC permissions |
| Day 5 | Replicas, ReplicaSet, Ingress, egress, probes, and Helm | Build an ecommerce routing project with raw manifests and Helm |
| Day 6 | Kubernetes networking | Test Pod networking, Service DNS, NodePort, ExternalName, CNI, and NetworkPolicy |
| Day 7 | ConfigMaps, Secrets, storage, and debugging | Inject configuration, mount storage, and troubleshoot common Kubernetes errors |

## Current Lab Status

Day 1 has been completed locally with Minikube.

```text
Namespace: day1
Pod: nginx-pod
Status: validated previously
```

Day 2 has been completed locally with Minikube.

```text
Namespace: day2
Deployment: nginx-deployment
Scale, rollout, rollback, and self-healing tested previously
```

Day 3 has been completed locally with Minikube.

```text
Namespace: day3
Deployment: web
ClusterIP and NodePort Services tested previously
EndpointSlices and port-forwarding validated previously
```

Day 4 has been completed locally with Minikube.

```text
Namespace: day4
Labeled Pods, Service selectors, ReplicaSet, Metrics Server, HPA, and RBAC tested previously
```

Day 5 project has been prepared for class.

```text
Namespace: day5
Project: ecommerce routing project
Raw manifests: replicas, ReplicaSet, Services, Ingress, probes, egress client
Helm chart: day5/helm/day5-ecommerce
Offline validation: raw manifests parsed successfully; Helm metadata parsed successfully
Cluster status after cleanup: Minikube stopped, no lab Pods running
```

Day 6 networking project has been prepared for class.

```text
Namespace: day6
Project: Kubernetes networking deep dive
Raw manifests: frontend, backend, Services, ExternalName, debug client, NetworkPolicy
Cluster note: use Minikube with Calico CNI for the NetworkPolicy practical
```

Day 7 configuration, storage, and debugging project has been prepared for class.

```text
Namespace: day7
Project: ConfigMap, Secret, PVC, emptyDir, and debugging lab
Raw manifests: ConfigMap, Secret, PVC, Deployment, emptyDir Pod
Debug manifests: ImagePullBackOff, CrashLoopBackOff, CreateContainerConfigError, Pending, readiness failure, OOMKilled
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

Run the Day 5 project with raw manifests:

```powershell
minikube addons enable ingress
kubectl apply -f day5/manifests/00-namespace.yaml
kubectl apply -f day5/manifests/01-payments-deployment.yaml
kubectl apply -f day5/manifests/02-payments-service.yaml
kubectl apply -f day5/manifests/03-orders-deployment.yaml
kubectl apply -f day5/manifests/04-orders-service.yaml
kubectl apply -f day5/manifests/05-frontend-replicaset.yaml
kubectl apply -f day5/manifests/06-ingress.yaml
kubectl apply -f day5/manifests/07-egress-test-pod.yaml
kubectl get all -n day5
kubectl get ingress -n day5
```

Run the Day 5 project with Helm:

```powershell
helm template day5-web day5/helm/day5-ecommerce
helm install day5-web day5/helm/day5-ecommerce
helm list -n day5
helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
helm history day5-web -n day5
helm rollback day5-web 1 -n day5
helm uninstall day5-web -n day5
```

Clean up Day 5:

```powershell
kubectl delete namespace day5 --ignore-not-found=true
minikube addons disable ingress
minikube stop
```

Run the Day 6 networking practical:

```powershell
minikube delete
minikube start --driver=docker --cni=calico
kubectl apply -f day6/manifests/00-namespace.yaml
kubectl apply -f day6/manifests/01-frontend-deployment.yaml
kubectl apply -f day6/manifests/02-frontend-nodeport-service.yaml
kubectl apply -f day6/manifests/03-backend-deployment.yaml
kubectl apply -f day6/manifests/04-backend-clusterip-service.yaml
kubectl apply -f day6/manifests/05-externalname-service.yaml
kubectl apply -f day6/manifests/06-network-client-pod.yaml
kubectl get pods -n day6 -o wide
kubectl get svc -n day6
kubectl exec -n day6 network-client -- nslookup backend-service.day6.svc.cluster.local
kubectl exec -n day6 network-client -- wget -qO- http://backend-service
minikube service frontend-nodeport -n day6 --url
```

Clean up Day 6:

```powershell
kubectl delete namespace day6 --ignore-not-found=true
minikube stop
```

Run the Day 7 ConfigMaps, Secrets, storage, and debugging practical:

```powershell
minikube start --driver=docker
kubectl apply -f day7/manifests/00-namespace.yaml
kubectl apply -f day7/manifests/01-configmap.yaml
kubectl apply -f day7/manifests/02-secret.yaml
kubectl apply -f day7/manifests/03-pvc.yaml
kubectl apply -f day7/manifests/04-config-secret-storage-deployment.yaml
kubectl apply -f day7/manifests/05-emptydir-pod.yaml
kubectl get all -n day7
kubectl get pvc -n day7
kubectl describe pod -n day7 -l app=config-storage-demo
kubectl logs -n day7 -l app=config-storage-demo
```

Run the Day 7 debugging examples one at a time:

```powershell
kubectl apply -f day7/debug/01-imagepullbackoff.yaml
kubectl describe pod imagepull-error-demo -n day7
kubectl get events -n day7 --sort-by=.metadata.creationTimestamp
kubectl delete -f day7/debug/01-imagepullbackoff.yaml
```

Clean up Day 7:

```powershell
kubectl delete namespace day7 --ignore-not-found=true
minikube stop
```

## Detailed Notes

- [Day 1: Kubernetes Basics, Architecture, Setup, Namespace, and Pod](day1/README.md)
- [Day 2: YAML, Labels, Selectors, ReplicaSets, and Deployments](day2/README.md)
- [Day 3: Services and Kubernetes Networking](day3/README.md)
- [Day 4: Labels, Selectors, ReplicaSet, HPA, Metrics Server, and RBAC](day4/README.md)
- [Day 5: Replicas, ReplicaSet, Ingress, Probes, and Helm](day5/README.md)
- [Day 6: Kubernetes Networking Deep Dive](day6/README.md)
- [Day 7: ConfigMaps, Secrets, Storage, and Debugging](day7/README.md)
- [Full 7-Day Roadmap](kubernetes-zero-to-hero-7-days.md)



