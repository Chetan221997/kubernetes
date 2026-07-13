# Kubernetes Zero To Hero

A practical Kubernetes learning repository focused on real-world workflows, clear architecture notes, and repeatable hands-on labs.

## Learning Objectives

By the end of this course, you should be able to:

- Explain Kubernetes architecture and core components.
- Deploy workloads using YAML manifests.
- Use labels and selectors to group and filter resources.
- Expose applications with Kubernetes Services.
- Manage replicas using ReplicaSets and Deployments.
- Configure Horizontal Pod Autoscaling with Metrics Server.
- Apply basic RBAC permissions using Roles and RoleBindings.
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
|   |-- dev-user-serviceaccount.yaml
|   |-- pod-reader-role.yaml
|   |-- pod-reader-rolebinding.yaml
|   |-- node-reader-clusterrole.yaml
|   |-- node-reader-clusterrolebinding.yaml
```

## 7-Day Roadmap

| Day | Focus Area | Practical Outcome |
| --- | --- | --- |
| Day 1 | Kubernetes architecture, Minikube setup, namespaces, pods | Run the first nginx pod from YAML |
| Day 2 | YAML structure, labels, selectors, ReplicaSets, Deployments | Deploy and scale nginx with a Deployment |
| Day 3 | Services and networking | Expose an application with ClusterIP, NodePort, and port-forwarding |
| Day 4 | Labels, selectors, ReplicaSet, HPA, Metrics Server, and RBAC | Test selector filtering, ReplicaSet self-healing, autoscaling, and RBAC permissions |
| Day 5 | ConfigMaps, Secrets, and storage | Inject configuration and sensitive data into workloads |
| Day 6 | Probes, resources, debugging, Ingress, and DaemonSets | Add health checks, debug failures, and review production features |
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

Day 2 has been completed locally with Minikube.

Validated workload:

```text
Namespace: day2
Manifest: day2/nginx-deployment.yaml
Deployment: nginx-deployment
Initial replicas: 3/3 available
Scale test: 3 -> 5 -> 2 replicas
Rolling update: nginx:1.27 -> nginx:1.28
Rollback: nginx:1.28 -> nginx:1.27
Self-healing: deleted one pod and ReplicaSet created a replacement
Container check after rollback: nginx/1.27.5
```

Day 3 has been completed locally with Minikube.

Validated workload:

```text
Namespace: day3
Deployment: web
Pods: 3/3 Running
ClusterIP Service: web-clusterip, 10.102.38.244:80
NodePort Service: web-nodeport, 80:30080/TCP
Endpoints: 10.244.0.23:80, 10.244.0.24:80, 10.244.0.25:80
EndpointSlices: populated for both Services
Internal Service test: Welcome to nginx!
NodePort test from Minikube node: HTTP/1.1 200 OK
Host access test with port-forward: HTTP 200, Welcome to nginx!
```

Day 4 has been completed locally with Minikube.

Validated workload:

```text
Namespace: day4
Labeled Pods: 4/4 Running
Service selector: ecommerce-frontend selected only frontend ecommerce Pods
ReplicaSet: nginx-rs maintained 3/3 Pods
Self-healing: deleted nginx-rs-28jd4 and replacement nginx-rs-vpq5f was created
Metrics Server: Running
HPA: php-apache scaled from 1 to 5 replicas under CPU load
RBAC: dev-user can list Pods, cannot delete Pods, and can list Nodes after ClusterRoleBinding
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

Run the Day 2 Deployment lab:

```powershell
kubectl create namespace day2
kubectl apply -f day2/nginx-deployment.yaml
kubectl get deployment -n day2
kubectl get replicaset -n day2
kubectl get pods -n day2 -o wide
kubectl scale deployment nginx-deployment --replicas=5 -n day2
kubectl set image deployment/nginx-deployment nginx=nginx:1.28 -n day2
kubectl rollout status deployment/nginx-deployment -n day2
kubectl rollout undo deployment/nginx-deployment -n day2
```

Run the Day 3 Services lab:

```powershell
kubectl create namespace day3
kubectl apply -f day3/web-deployment.yaml
kubectl apply -f day3/clusterip-service.yaml
kubectl apply -f day3/nodeport-service.yaml
kubectl get deployment,svc,pods -n day3 -o wide
kubectl get endpointslice -n day3
kubectl run network-client -n day3 --image=busybox:1.36 --restart=Never --command -- sleep 3600
kubectl exec network-client -n day3 -- wget -qO- http://web-clusterip
minikube ip
minikube ssh -- curl -I http://<minikube-ip>:30080/
# Terminal 1 - keep this running
kubectl port-forward svc/web-clusterip -n day3 8080:80

# Terminal 2 - test while port-forward is running
Invoke-WebRequest -UseBasicParsing http://127.0.0.1:8080/
```

Run the Day 4 Labels, HPA, and RBAC lab:

```powershell
kubectl create namespace day4
kubectl apply -f day4/labeled-pods.yaml
kubectl apply -f day4/ecommerce-frontend-service.yaml
kubectl get pods -n day4 --show-labels
kubectl get pods -n day4 -l environment=dev
kubectl get pods -n day4 -l 'environment in (dev,qa)'
kubectl get endpointslice -n day4 -l kubernetes.io/service-name=ecommerce-frontend
kubectl apply -f day4/nginx-replicaset.yaml
kubectl get rs -n day4
kubectl delete pod <nginx-rs-pod-name> -n day4
minikube addons enable metrics-server
kubectl apply -f day4/php-apache-deployment.yaml
kubectl apply -f day4/php-apache-service.yaml
kubectl apply -f day4/php-apache-hpa.yaml
kubectl get hpa -n day4
kubectl apply -f day4/dev-user-serviceaccount.yaml
kubectl apply -f day4/pod-reader-role.yaml
kubectl apply -f day4/pod-reader-rolebinding.yaml
kubectl auth can-i list pods --as=system:serviceaccount:day4:dev-user -n day4
kubectl auth can-i delete pods --as=system:serviceaccount:day4:dev-user -n day4
kubectl apply -f day4/node-reader-clusterrole.yaml
kubectl apply -f day4/node-reader-clusterrolebinding.yaml
kubectl auth can-i list nodes --as=system:serviceaccount:day4:dev-user
```

Clean up the Day 1 lab:

```powershell
kubectl delete -f day1/nginx-pod.yaml
kubectl delete namespace day1
```

Clean up the Day 2 lab:

```powershell
kubectl delete namespace day2
```

Clean up the Day 3 lab:

```powershell
kubectl delete namespace day3
```

Clean up the Day 4 lab:

```powershell
kubectl delete namespace day4
kubectl delete clusterrolebinding day4-node-reader-binding
kubectl delete clusterrole day4-node-reader
```

## Detailed Notes

- [Day 1: Kubernetes Basics, Architecture, Setup, Namespace, and Pod](day1/README.md)
- [Day 2: YAML, Labels, Selectors, ReplicaSets, and Deployments](day2/README.md)
- [Day 3: Services and Kubernetes Networking](day3/README.md)
- [Day 4: Labels, Selectors, ReplicaSet, HPA, Metrics Server, and RBAC](day4/README.md)
- [Full 7-Day Roadmap](kubernetes-zero-to-hero-7-days.md)
