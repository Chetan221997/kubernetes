# Kubernetes Zero To Hero - 7 Day Roadmap

This document defines a practical Kubernetes learning plan. Each day includes theory, commands, YAML manifests, troubleshooting, and a concrete lab outcome.

## Method

```text
Concept -> Architecture -> YAML -> Commands -> Validation -> Troubleshooting -> Interview Review
```

## Lab Environment

- Operating system: Windows with PowerShell
- Container runtime: Docker Desktop
- Local cluster: Minikube
- Kubernetes CLI: kubectl
- Package manager for Kubernetes: Helm

## Core Kubernetes Flow

```text
kubectl -> kube-api-server -> scheduler/controller -> kubelet -> container runtime -> pod
```

## Day 1 - Architecture, Setup, Namespace, Pod

Focus:

- Kubernetes purpose and architecture.
- Control plane components.
- Worker node components.
- Minikube setup.
- Namespace basics.
- First Pod manifest.

Practical outcome:

- Start a local Minikube cluster.
- Create the `day1` namespace.
- Deploy `nginx-pod` using `day1/nginx-pod.yaml`.
- Inspect Pod status, events, logs, and container command execution.

Detailed module: [day1/README.md](day1/README.md)

## Day 2 - YAML, Labels, Selectors, ReplicaSets, Deployments

Focus:

- Kubernetes manifest structure.
- Labels and selectors.
- ReplicaSet behavior.
- Deployment rollouts and rollback.

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

## Day 6 - Kubernetes Networking

Focus:

- Kubernetes networking meaning and purpose.
- Container-to-container communication inside one Pod.
- Pod-to-Pod communication.
- Pod-to-Service communication.
- Service DNS and CoreDNS.
- ClusterIP, NodePort, LoadBalancer, and ExternalName Services.
- Ingress traffic and egress traffic.
- Cluster networking with CNI plugins.
- Common CNI plugins: Flannel, Calico, Weave Net, and Cilium.
- Pod CIDR, Service CIDR, node IPs, Pod IPs, and Service IPs.
- NetworkPolicy behavior using Calico on Minikube.
- Networking troubleshooting using `get`, `describe`, `exec`, `nslookup`, `wget`, and events.

Practical outcome:

- Start Minikube with Calico CNI.
- Deploy frontend and backend workloads.
- Expose frontend using NodePort.
- Expose backend using ClusterIP.
- Test backend DNS from a client Pod.
- Test ExternalName DNS behavior.
- Inspect Pod IPs, Service IPs, and node IPs.
- Apply an egress deny NetworkPolicy.
- Apply an allow policy for DNS and backend traffic.
- Debug common Service, DNS, NodePort, and policy issues.

Key commands:

```powershell
minikube delete
minikube start --driver=docker --cni=calico
kubectl cluster-info
kubectl get pods -n kube-system
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
kubectl exec -n day6 network-client -- nslookup external-api.day6.svc.cluster.local
minikube service frontend-nodeport -n day6 --url
kubectl apply -f day6/manifests/07-deny-egress-networkpolicy.yaml
kubectl apply -f day6/manifests/08-allow-dns-and-backend-egress.yaml
kubectl get networkpolicy -n day6
kubectl delete namespace day6 --ignore-not-found=true
minikube stop
```

Detailed module: [day6/README.md](day6/README.md)

## Day 7 - ConfigMaps, Secrets, Storage, And Debugging

Focus:

- ConfigMaps for non-sensitive application configuration.
- Secrets for sensitive application configuration.
- Environment variable injection from ConfigMaps and Secrets.
- Mounting ConfigMaps and Secrets as files.
- Storage basics: volumes, `emptyDir`, PersistentVolume, PersistentVolumeClaim, StorageClass, and CSI drivers.
- PVC-based storage for application data.
- Temporary shared storage using `emptyDir`.
- Common Kubernetes errors and how to debug them.
- Debug flow: status, describe, logs, previous logs, events, exec, YAML inspection, and resource checks.

Practical outcome:

- Create a ConfigMap with application settings.
- Create a Secret using `stringData`.
- Create a PersistentVolumeClaim.
- Deploy a workload that consumes ConfigMap, Secret, and PVC data.
- Mount ConfigMap and Secret values as files.
- Use `emptyDir` between two containers in one Pod.
- Run controlled debugging examples for common error states.
- Understand how to read Kubernetes events and failure messages.

Common errors covered:

- `ImagePullBackOff` and `ErrImagePull`.
- `CrashLoopBackOff`.
- `CreateContainerConfigError`.
- `Pending` Pod due to storage or scheduling issues.
- `ContainerCreating` stuck.
- `OOMKilled`.
- Readiness probe failures.
- Service with no endpoints.
- `Forbidden` RBAC errors.
- YAML validation errors.

Key commands:

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
kubectl exec -n day7 deploy/config-storage-demo -- printenv APP_MODE
kubectl exec -n day7 deploy/config-storage-demo -- cat /etc/app-config/app.properties
kubectl exec -n day7 deploy/config-storage-demo -- cat /data/status.txt
kubectl logs emptydir-demo -n day7 -c reader
```

Debug examples:

```powershell
kubectl apply -f day7/debug/01-imagepullbackoff.yaml
kubectl describe pod imagepull-error-demo -n day7
kubectl get events -n day7 --sort-by=.metadata.creationTimestamp
kubectl delete -f day7/debug/01-imagepullbackoff.yaml

kubectl apply -f day7/debug/02-crashloopbackoff.yaml
kubectl logs crashloop-demo -n day7 --previous
kubectl delete -f day7/debug/02-crashloopbackoff.yaml

kubectl apply -f day7/debug/03-createcontainerconfigerror.yaml
kubectl describe pod missing-secret-demo -n day7
kubectl delete -f day7/debug/03-createcontainerconfigerror.yaml
```

Cleanup:

```powershell
kubectl delete namespace day7 --ignore-not-found=true
minikube stop
```

Detailed module: [day7/README.md](day7/README.md)




