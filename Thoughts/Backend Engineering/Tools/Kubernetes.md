# Kubernetes

> *Container orchestration at scale*

---

## Why Kubernetes?

- **Scaling** - Automatic pod scaling
- **Self-healing** - Restart failed containers
- **Load balancing** - Distribute traffic
- **Rollouts** - Zero-downtime deployments

---

## Architecture

```
┌─────────────────────────────────────────────────┐
│                 Kubernetes Cluster              │
├─────────────────────────────────────────────────┤
│  ┌─────────────┐   ┌─────────────────────────┐  │
│  │Control Plane│   │        Nodes            │  │
│  ├─────────────┤   ├───────────┬─────────────┤  │
│  │ API Server  │   │  Node 1   │   Node 2    │  │
│  │ Scheduler   │   │ ┌───────┐ │ ┌───────┐   │  │
│  │ Controller  │   │ │ Pod 1 │ │ │ Pod 3 │   │  │
│  │ etcd        │   │ │ Pod 2 │ │ │ Pod 4 │   │  │
│  └─────────────┘   │ └───────┘ │ └───────┘   │  │
│                    └───────────┴─────────────┘  │
└─────────────────────────────────────────────────┘
```

---

## Key Resources

| Resource | Purpose |
|----------|---------|
| **Pod** | Smallest unit, 1+ containers |
| **Deployment** | Manages pod replicas |
| **Service** | Network access to pods |
| **Ingress** | External HTTP routing |
| **ConfigMap** | Configuration data |
| **Secret** | Sensitive data |

---

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        ports:
        - containerPort: 8000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer
```

---

## Commands

```bash
# Apply configuration
kubectl apply -f deployment.yaml

# Get resources
kubectl get pods
kubectl get services

# Scale
kubectl scale deployment myapp --replicas=5

# Logs
kubectl logs <pod-name>

# Exec into pod
kubectl exec -it <pod-name> -- /bin/sh
```

---

*Back to: [[Index|Tools Home]]*
