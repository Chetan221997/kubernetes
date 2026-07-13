# Kubernetes Zero To Hero - 7 Day Roadmap

This document defines a practical Kubernetes learning plan. Each day includes theory, commands, YAML manifests, troubleshooting, and a concrete lab outcome.

## Method

```text
Concept ---> Architecture ---> YAML ---> Commands ---> Validation ---> Troubleshooting ---> Interview Review
```

## Lab Environment

- Operating system: Windows with PowerShell
- Container runtime: Docker Desktop
- Local cluster: Minikube
- Kubernetes CLI: kubectl
- Package manager for Kubernetes: Helm

## Core Kubernetes Flow

```text
kubectl ---> kube-api-server ---> scheduler/controller ---> kubelet ---> container runtime ---> pod
```

## Day 1 - Architecture, Setup, Namespace, Pod

Focus:

- Kubernetes purpose and architecture
- Control plane components
- Worker node components
- Minikube setup
- Namespace basics
- First pod manifest

Practical outcome:

- Start a local Minikube cluster.
- Create the `day1` namespace.
- Deploy `nginx-pod` using `day1/nginx-pod.yaml`.
- Inspect pod status, events, logs, and container command execution.

Detailed module: [day1/README.md](day1/README.md)

## Day 2 - YAML, Labels, Selectors, ReplicaSets, Deployments

Focus:

- Kubernetes manifest structure
- Labels and selectors
- ReplicaSet behavior
- Deployment rollouts and rollback

Practical outcome:

- Create an nginx Deployment.
- Scale replicas.
- Update the container image.
- Review rollout history and rollback.

Detailed module: [day2/README.md](day2/README.md)

## Day 3 - Services And Networking

Focus:

- Pod IP behavior and why Pod IPs should not be used directly.
- Service selectors and Pod labels.
- ClusterIP, NodePort, LoadBalancer, and ExternalName Service types.
- `port`, `targetPort`, and `nodePort`.
- Endpoints and EndpointSlices.
- Internal and external Service testing.

Practical outcome:

- Create a web Deployment with 3 nginx Pods.
- Expose it internally with a ClusterIP Service.
- Verify endpoints and EndpointSlices.
- Test internal access from a temporary BusyBox Pod.
- Expose it with a NodePort Service.
- Validate NodePort from the Minikube node.
- Validate host access using `kubectl port-forward`.

Detailed module: [day3/README.md](day3/README.md)

## Day 4 - Labels, Selectors, ReplicaSet, HPA, Metrics Server, And RBAC

Focus:

- Label design for real workloads.
- Equality-based selectors.
- Set-based selectors.
- Service selectors and EndpointSlices.
- ReplicaSet self-healing.
- Metrics Server.
- Horizontal Pod Autoscaler.
- Role, RoleBinding, ClusterRole, and ClusterRoleBinding.

Practical outcome:

- Create labeled ecommerce Pods.
- Filter Pods using equality-based and set-based selectors.
- Create a Service that selects only frontend ecommerce Pods.
- Create a ReplicaSet and test self-healing.
- Enable Metrics Server.
- Create an HPA and generate CPU load.
- Validate namespace and cluster RBAC permissions.

Detailed module: [day4/README.md](day4/README.md)

## Day 5 - Replicas, ReplicaSet, Ingress, Egress, Probes, And Helm

Focus:

- `replicas` in a manifest.
- ReplicaSet desired state and self-healing.
- Ingress traffic vs egress traffic.
- Ingress resource vs Ingress controller.
- NGINX Ingress Controller in Minikube.
- HTTP path-based routing.
- Liveness probes.
- Readiness probes.
- Helm chart structure.
- Helm install, list, upgrade, history, rollback, and uninstall.

Practical outcome:

- Build an ecommerce routing project with payments and orders services.
- Route `/payments` and `/orders` through Kubernetes Ingress.
- Add liveness and readiness probes to application Deployments.
- Create a standalone ReplicaSet and test replacement behavior.
- Test egress traffic from a Pod.
- Package the same project as a Helm chart.

Key commands:

```powershell
minikube start --driver=docker
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
kubectl describe ingress ecommerce-ingress -n day5
minikube ip
curl.exe --resolve day5.local:80:<minikube-ip> http://day5.local/payments
curl.exe --resolve day5.local:80:<minikube-ip> http://day5.local/orders
kubectl exec -n day5 egress-client -- wget -qO- http://example.com
helm template day5-web day5/helm/day5-ecommerce
helm install day5-web day5/helm/day5-ecommerce
helm list -n day5
helm upgrade day5-web day5/helm/day5-ecommerce --set replicaCount=3
helm history day5-web -n day5
helm rollback day5-web 1 -n day5
helm uninstall day5-web -n day5
```

Detailed module: [day5/README.md](day5/README.md)

## Day 6 - ConfigMaps, Secrets, Storage, And Debugging

Focus:

- Externalizing configuration.
- Handling sensitive values.
- Environment variable injection.
- Volumes and PersistentVolumeClaims.
- Common Pod failure states.
- Debugging with logs, describe, events, and exec.

Practical outcome:

- Create a ConfigMap.
- Create a Secret.
- Inject both into a workload.
- Mount a volume for persistent data.
- Break and debug a workload intentionally.

Key commands:

```powershell
kubectl create namespace day6
kubectl create configmap app-config --from-literal=APP_MODE=dev -n day6
kubectl create secret generic app-secret --from-literal=DB_PASSWORD=admin123 -n day6
kubectl apply -f day6/config-demo.yaml
kubectl describe pod <pod-name> -n day6
kubectl logs <pod-name> -n day6
kubectl get events -n day6 --sort-by=.metadata.creationTimestamp
kubectl exec -it <pod-name> -n day6 -- sh
```

## Day 7 - Final Project And Production Review

Focus:

- Review architecture.
- Review workloads and Services.
- Review Ingress and probes.
- Review Helm packaging.
- Production readiness checklist.
- Final Kubernetes project.

Practical outcome:

- Build a final Kubernetes application from scratch.
- Package it with Helm.
- Validate Services, Ingress, probes, and rollback.
- Review interview questions from Day 1 to Day 7.

Key commands:

```powershell
helm template final-app ./final-app
helm install final-app ./final-app
helm upgrade final-app ./final-app
helm history final-app
helm rollback final-app 1
helm uninstall final-app
```
