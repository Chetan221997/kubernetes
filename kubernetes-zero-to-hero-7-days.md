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
kubectl apply -f day2/nginx-deployment.yaml
kubectl get deploy,rs,pods -n day2
kubectl scale deployment nginx-deployment --replicas=5 -n day2
kubectl rollout status deployment/nginx-deployment -n day2
kubectl rollout undo deployment/nginx-deployment -n day2
```

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

Key commands:

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

Key commands:

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
kubectl run load-generator -n day4 --image=busybox:1.36 --restart=Never --command -- /bin/sh -c "while true; do wget -q -O- http://php-apache; done"
kubectl get hpa php-apache -n day4
kubectl delete pod load-generator -n day4
kubectl apply -f day4/dev-user-serviceaccount.yaml
kubectl apply -f day4/pod-reader-role.yaml
kubectl apply -f day4/pod-reader-rolebinding.yaml
kubectl auth can-i list pods --as=system:serviceaccount:day4:dev-user -n day4
kubectl auth can-i delete pods --as=system:serviceaccount:day4:dev-user -n day4
kubectl apply -f day4/node-reader-clusterrole.yaml
kubectl apply -f day4/node-reader-clusterrolebinding.yaml
kubectl auth can-i list nodes --as=system:serviceaccount:day4:dev-user
```

Detailed module: [day4/README.md](day4/README.md)

## Day 5 - ConfigMaps, Secrets, And Storage

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
kubectl create namespace day5
kubectl create configmap app-config --from-literal=APP_MODE=dev -n day5
kubectl create secret generic app-secret --from-literal=DB_PASSWORD=admin123 -n day5
kubectl apply -f day5/config-demo.yaml
kubectl logs deployment/config-demo -n day5
```

## Day 6 - Probes, Resources, Debugging, Ingress, And DaemonSets

Focus:

- Liveness probes
- Readiness probes
- Startup probes
- CPU and memory requests
- CPU and memory limits
- Common pod failure states
- Ingress routing basics
- DaemonSet use cases

Practical outcome:

- Add health probes to a workload.
- Add resource requests and limits.
- Intentionally break an image name.
- Diagnose the issue using events and `describe`.
- Review Ingress and DaemonSet production use cases.

Key commands:

```powershell
kubectl describe pod <pod-name> -n day6
kubectl logs <pod-name> -n day6
kubectl get events -n day6 --sort-by=.metadata.creationTimestamp
kubectl exec -it <pod-name> -n day6 -- sh
```

## Day 7 - Helm And Final Project

Focus:

- Helm chart structure
- `values.yaml`
- Template rendering
- Install, upgrade, and rollback
- Final Kubernetes project review

Practical outcome:

- Convert a Kubernetes workload into a Helm chart.
- Install the chart locally.
- Change values and upgrade the release.
- Perform rollback.
- Review all concepts from Day 1 to Day 7.

Key commands:

```powershell
helm create final-app
helm template final-app
helm install final-app ./final-app
helm upgrade final-app ./final-app
helm rollback final-app 1
```
