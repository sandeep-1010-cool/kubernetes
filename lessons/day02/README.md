# Day 02: Pods & Deployments

## 🎯 What are Pods?

**Pods** are the smallest deployable units in Kubernetes - think of them as "wrappers" around containers.

## 📦 Pod Basics

### Pod Lifecycle
- **Pending** → **Running** → **Succeeded/Failed**
- Pods are **ephemeral** - they can be killed anytime
- **One IP per pod** - containers in same pod share network

### Pod YAML Structure
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

## 🚀 Quick Pod Commands

```bash
# Create pod from YAML
kubectl apply -f pod.yaml

# Run pod directly
kubectl run my-pod --image=nginx

# View pods
kubectl get pods

# Get pod details
kubectl describe pod <pod-name>

# Delete pod
kubectl delete pod <pod-name>
```

## 🔄 ReplicaSets

**ReplicaSets** ensure a specified number of pod replicas are running.

### ReplicaSet YAML
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
```

### ReplicaSet Commands
```bash
# Create ReplicaSet
kubectl apply -f replicaset.yaml

# Scale ReplicaSet
kubectl scale replicaset my-replicaset --replicas=5

# View ReplicaSets
kubectl get rs
```

## 🎯 Deployments

**Deployments** manage ReplicaSets and provide rolling updates.

### Deployment Benefits
- ✅ **Rolling updates** - zero downtime deployments
- ✅ **Rollback** - revert to previous version
- ✅ **Scaling** - easy to scale up/down
- ✅ **Health checks** - automatic restart on failure

### Deployment YAML
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:1.20
        ports:
        - containerPort: 80
```

## 🔄 Rolling Updates

### Update Strategies
```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Extra pods during update
      maxUnavailable: 1  # Max unavailable pods
```

### Update Commands
```bash
# Update image
kubectl set image deployment/my-deployment my-container=nginx:1.21

# Check rollout status
kubectl rollout status deployment/my-deployment

# Rollback to previous version
kubectl rollout undo deployment/my-deployment

# View rollout history
kubectl rollout history deployment/my-deployment
```

## 📊 Quick Reference

| Resource | Purpose | Key Feature |
|----------|---------|-------------|
| **Pod** | Runs containers | Ephemeral, one IP |
| **ReplicaSet** | Ensures pod count | Scaling, self-healing |
| **Deployment** | Manages ReplicaSets | Rolling updates, rollback |

## 🚀 Essential Commands

```bash
# Create deployment
kubectl create deployment my-app --image=nginx

# Scale deployment
kubectl scale deployment my-app --replicas=5

# Update deployment
kubectl edit deployment my-app

# View deployment status
kubectl get deployments

# Check deployment events
kubectl describe deployment my-app
```

## 💡 Pro Tips

- **Always use Deployments** instead of Pods directly
- **Set resource limits** in pod specs for production
- **Use rolling updates** for zero-downtime deployments
- **Test rollbacks** before going to production
- **Monitor rollout status** during updates

## 🔍 Troubleshooting

```bash
# Check pod logs
kubectl logs <pod-name>

# Execute into pod
kubectl exec -it <pod-name> -- /bin/bash

# Check pod events
kubectl describe pod <pod-name>

# Force delete stuck pod
kubectl delete pod <pod-name> --force --grace-period=0
```

## 📝 Summary

- **Pod** = Container wrapper (ephemeral)
- **ReplicaSet** = Ensures pod count (scaling)
- **Deployment** = Manages ReplicaSets (updates)

**Next**: Day 03 - Services & Networking 