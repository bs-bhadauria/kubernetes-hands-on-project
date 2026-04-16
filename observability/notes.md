# Observability in Kubernetes (K9s)

## Objective

To observe how Kubernetes distributes traffic across multiple pods using Service-based load balancing.

---

## Tools Used

* K9s (Kubernetes CLI UI)
* kubectl
* curl

---

## Steps Performed

### 1. Open K9s

```bash
k9s
```

---

### 2. View Pods

* Navigated to running pods
* Identified load balancing pods:

```
nginx-lb-test-xxxxx
nginx-lb-test-yyyyy
nginx-lb-test-zzzzz
```

---

### 3. View Logs

* Selected a pod
* Pressed:

```
l
```

* Opened real-time logs

---

### 4. Generate Traffic

```bash
for i in {1..20}; do curl <minikube-ip>:30008; done
```

---

### 5. Observations

* Requests were distributed across multiple pods
* Each pod received different requests
* Logs confirmed:

```
GET / HTTP/1.1 200
```

* Multiple pods handled requests simultaneously

---

## Key Learning

* Kubernetes Service distributes traffic across pods
* Pods are interchangeable
* Load balancing happens automatically
* Observability helps in debugging and monitoring

---

## Conclusion

Kubernetes provides built-in load balancing, and tools like K9s help visualize traffic flow and pod-level activity in real time.
