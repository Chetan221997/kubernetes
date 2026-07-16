Create the namespace:
Command: kubectl create namespace day3

Set as the current namespace:
Command: kubectl config set-context --current --namespace=day3

Verify:
Command: kubectl get namespaces

<img width="1326" height="677" alt="image" src="https://github.com/user-attachments/assets/d60fc668-58ac-4e75-888c-b4f4482e6d2c" />

Create the YAML File:

Step 5: Create the YAML File

Open the editor:

vi labelled-pods.yaml

Press i to enter Insert Mode.

Paste the following YAML:

apiVersion: v1
kind: Pod
metadata:
  name: frontend-dev-pod
  labels:
    app: ecommerce
    environment: dev
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    ports:
    - containerPort: 80

---
apiVersion: v1
kind: Pod
metadata:
  name: backend-dev-pod
  labels:
    app: ecommerce
    environment: dev
    tier: backend
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    ports:
    - containerPort: 80

---
apiVersion: v1
kind: Pod
metadata:
  name: frontend-prod-pod
  labels:
    app: ecommerce
    environment: prod
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    ports:
    - containerPort: 80

Save the file:

Press Esc
Type:
:wq
Press Enter

Step 6: Verify the File Exists
ls

You should see:

labelled-pods.yaml

<img width="660" height="420" alt="image" src="https://github.com/user-attachments/assets/3f7b79e8-c09d-4d45-b156-f8730f0e7fe2" />

Step 7: Create the Pods

Run:

kubectl apply -f labelled-pods.yaml

Expected output:

pod/frontend-dev-pod created
pod/backend-dev-pod created
pod/frontend-prod-pod created
Step 8: Verify the Pods
kubectl get pods --show-labels

Expected output:

NAME                 READY   STATUS    LABELS
frontend-dev-pod     1/1     Running   app=ecommerce,environment=dev,tier=frontend
backend-dev-pod      1/1     Running   app=ecommerce,environment=dev,tier=backend
frontend-prod-pod    1/1     Running   app=ecommerce,environment=prod,tier=frontend

Step 9: Equality-Based Label Selectors
Development Pods
kubectl get pods -l environment=dev
Frontend Pods
kubectl get pods -l tier=frontend
Development Backend Pod
kubectl get pods -l environment=dev,tier=backend
Pods That Are Not Production
kubectl get pods -l environment!=prod

Step 10: Set-Based Label Selectors
Show Dev and Prod Pods
kubectl get pods -l "environment in (dev,prod)"
Exclude Backend Pods
kubectl get pods -l "tier notin (backend)"
Show Only Production Frontend Pod
kubectl get pods -l "environment in (prod),tier in (frontend)"

Step 11: Verify Namespace (Optional)
kubectl get all
or
kubectl get pods

Complete Command List:

minikube start
kubectl create namespace day3
kubectl config set-context --current --namespace=day3
cd ~/Downloads
vi labelled-pods.yaml
kubectl apply -f labelled-pods.yaml
kubectl get pods --show-labels
kubectl get pods -l environment=dev
kubectl get pods -l tier=frontend
kubectl get pods -l environment=dev,tier=backend
kubectl get pods -l environment!=prod
kubectl get pods -l "environment in (dev,prod)"
kubectl get pods -l "tier notin (backend)"
kubectl get pods -l "environment in (prod),tier in (frontend)"
