# Kubernetes Hands-on Project (Minikube)

This repository demonstrates a complete hands-on implementation of core Kubernetes concepts including deployment, scaling, load balancing, and observability using Minikube.

---

## Objectives

* Understand Kubernetes architecture and components
* Deploy containerized applications using Pods and Deployments
* Implement auto-healing and scaling
* Perform rolling updates and rollback
* Expose applications using Services
* Demonstrate load balancing across multiple pods
* Monitor traffic distribution using K9s

---

## Tech Stack

* Kubernetes
* Minikube
* Docker
* kubectl
* K9s
* YAML

---

## Project Structure

```
kubernetes-practice/
│
├── pod/
│   └── nginx-pod.yaml
│
├── deployment/
│   └── nginx-deployment.yaml
│
├── service-nodeport/
│   └── nginx-service.yaml
│
├── load-balancing/
│   ├── nginx-lb-test.yaml
│   └── nginx-lb-test-service.yaml
│
├── observability/
│   └── notes.md
│
└── README.md
```

---

## 1. Pod Creation

### nginx-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```

### Commands

```bash
kubectl apply -f nginx-pod.yaml
kubectl get pods
```

### Access (Port Forward)

```bash
kubectl port-forward pod/nginx-pod 8080:80
curl localhost:8080
```

---

## 2. Deployment (Auto-Healing & Scaling)

### nginx-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
```

### Commands

```bash
kubectl apply -f nginx-deployment.yaml
kubectl get deployments
kubectl get pods
```

### Auto-Healing Test

```bash
kubectl delete pod <pod-name>
```

Kubernetes automatically recreates the deleted pod.

---

## 📈 3. Scaling

```bash
kubectl scale deployment nginx-deployment --replicas=5
kubectl get pods
```

---

## 🔁 4. Rolling Updates & Rollback

### Update Image

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.25
```

### Watch Update

```bash
kubectl get pods -w
```

### Rollback

```bash
kubectl rollout undo deployment nginx-deployment
```

---

## 5. Service (NodePort)

### nginx-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

### Commands

```bash
kubectl apply -f nginx-service.yaml
kubectl get services
minikube ip
```

### Access

```bash
curl <minikube-ip>:30007
```

---

## 6. Load Balancing Demonstration

### Custom Deployment (Returns Pod Name)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-lb-test
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-lb-test
  template:
    metadata:
      labels:
        app: nginx-lb-test
    spec:
      containers:
      - name: nginx
        image: nginx
        command: ["/bin/sh", "-c"]
        args:
          - echo "Hello from $(hostname)" > /usr/share/nginx/html/index.html && nginx -g 'daemon off;'
        ports:
        - containerPort: 80
```

---

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb-test-service
spec:
  type: NodePort
  selector:
    app: nginx-lb-test
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```

---

### Test Load Balancing

```bash
for i in {1..10}; do curl <minikube-ip>:30008; done
```

### Output

```
Hello from nginx-lb-test-abc123
Hello from nginx-lb-test-def456
Hello from nginx-lb-test-xyz789
```

This confirms traffic is distributed across multiple pods.

---

## 7. Observability (K9s)

### Run K9s

```bash
k9s
```

### View Logs

* Select Pod
* Press `l`

### Generate Traffic

```bash
for i in {1..20}; do curl <minikube-ip>:30008; done
```

Logs will show requests hitting different pods.

---

## Key Learnings

* Kubernetes ensures high availability using ReplicaSets
* Deployments provide auto-healing and scaling
* Rolling updates ensure zero downtime
* Services provide internal load balancing
* NodePort exposes applications externally
* Observability tools help in debugging and monitoring

---

## ❗ Common Issues & Fixes

| Issue              | Fix                               |
| ------------------ | --------------------------------- |
| Pod not accessible | Use port-forward                  |
| Connection refused | Restart Minikube                  |
| Wrong logs         | Check correct pod labels          |
| Download issues    | Use browser instead of curl       |
| File not found     | Verify filename before extraction |

---

## Future Enhancements

* Implement Ingress Controller
* Use ConfigMaps and Secrets
* Deploy on AWS EKS / GCP GKE
* Implement CI/CD pipeline

---

## Author

**Bhoopendra Singh Bhadauria**
