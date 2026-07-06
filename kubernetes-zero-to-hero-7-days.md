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

Key commands:

```powershell
kubectl create namespace day2
kubectl apply -f day2/deployment.yaml
kubectl get deploy,rs,pods -n day2
kubectl scale deployment nginx-deployment --replicas=5 -n day2
kubectl rollout status deployment/nginx-deployment -n day2
kubectl rollout undo deployment/nginx-deployment -n day2
```

## Day 3 - Services And Networking

Focus:

- Pod IP behavior
- Service selectors
- ClusterIP, NodePort, LoadBalancer, ExternalName
- Port, targetPort, and nodePort

Practical outcome:

- Expose a Deployment with a ClusterIP Service.
- Access it using `kubectl port-forward`.
- Create a NodePort Service and inspect endpoints.

Key commands:

```powershell
kubectl get svc -n day3
kubectl get endpoints -n day3
kubectl describe svc web-service -n day3
kubectl port-forward svc/web-service 8080:80 -n day3
```

## Day 4 - ConfigMaps, Secrets, And Storage

Focus:

- Externalizing configuration
- Handling sensitive values
- Environment variable injection
- Volumes and PersistentVolumeClaims

Practical outcome:

- Create a ConfigMap.
- Create a Secret.
- Inject both into a workload.
- Mount a volume for persistent data.

Key commands:

```powershell
kubectl create configmap app-config --from-literal=APP_MODE=dev -n day4
kubectl create secret generic app-secret --from-literal=DB_PASSWORD=admin123 -n day4
kubectl apply -f day4/config-demo.yaml
kubectl logs deployment/config-demo -n day4
```

## Day 5 - Probes, Resources, And Debugging

Focus:

- Liveness probes
- Readiness probes
- Startup probes
- CPU and memory requests
- CPU and memory limits
- Common pod failure states

Practical outcome:

- Add health probes to a workload.
- Add resource requests and limits.
- Intentionally break an image name.
- Diagnose the issue using events and `describe`.

Key commands:

```powershell
kubectl describe pod <pod-name> -n day5
kubectl logs <pod-name> -n day5
kubectl get events -n day5 --sort-by=.metadata.creationTimestamp
kubectl exec -it <pod-name> -n day5 -- sh
```

## Day 6 - Ingress, Autoscaling, DaemonSets, RBAC

Focus:

- Ingress routing
- Ingress controllers
- Horizontal Pod Autoscaler
- DaemonSet use cases
- Role Based Access Control

Practical outcome:

- Create an Ingress rule.
- Create a DaemonSet.
- Review HPA requirements.
- Explain Role, RoleBinding, ClusterRole, and ClusterRoleBinding.

Key commands:

```powershell
kubectl get ingress -n day6
kubectl get daemonset -n day6
kubectl get pods -n day6 -o wide
kubectl autoscale deployment web --cpu-percent=50 --min=1 --max=10 -n day6
kubectl get hpa -n day6
```

## Day 7 - Helm And Final Project

Focus:

- Helm chart structure
- values.yaml
- Release lifecycle
- Final multi-component application

Practical outcome:

- Package Kubernetes manifests as a Helm chart.
- Install, upgrade, review history, rollback, and uninstall a release.
- Deploy a final application with frontend, backend, database, config, secrets, services, and health checks.

Key commands:

```powershell
helm create todo-app
helm install todo ./todo-app
helm upgrade todo ./todo-app
helm history todo
helm rollback todo 1
helm uninstall todo
```

## Final Capability Checklist

- Explain Kubernetes architecture.
- Create and apply YAML manifests.
- Deploy pods and deployments.
- Expose workloads with services.
- Manage configuration and secrets.
- Add health checks and resource controls.
- Debug pod startup and runtime issues.
- Understand ingress, autoscaling, DaemonSets, and RBAC.
- Package workloads using Helm.
