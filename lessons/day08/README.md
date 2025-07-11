# Day 08: Taints, Tolerations, Affinity

## 🎯 What are Taints & Tolerations?

**Taints** repel pods from nodes. **Tolerations** allow pods to run on tainted nodes. **Affinity** attracts pods to specific nodes.

## 🚫 Taints & Tolerations

### Taint Effects
- **NoSchedule**: Pods cannot be scheduled (hard rule)
- **PreferNoSchedule**: Pods prefer not to be scheduled (soft rule)
- **NoExecute**: Pods are evicted if already running

### Common Use Cases
- **Dedicated nodes** for specific workloads
- **GPU nodes** for ML workloads
- **Spot instances** for cost optimization
- **Maintenance windows** for node updates

## 🚀 Quick Commands

### Taint Commands
```bash
# Add taint to node
kubectl taint nodes node1 key=value:NoSchedule

# Remove taint from node
kubectl taint nodes node1 key:NoSchedule-

# View node taints
kubectl describe node node1 | grep Taints

# Add taint with effect
kubectl taint nodes node1 dedicated=gpu:NoSchedule
```

### Toleration Commands
```bash
# Check pod tolerations
kubectl get pod my-pod -o yaml | grep -A 10 tolerations

# Check node taints
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
```

## 📋 Taint & Toleration Examples

### Node Taint
```yaml
apiVersion: v1
kind: Node
metadata:
  name: gpu-node
spec:
  taints:
  - key: dedicated
    value: gpu
    effect: NoSchedule
```

### Pod Toleration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
  - name: gpu-app
    image: nvidia/cuda
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

### Multiple Tolerations
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: flexible-pod
spec:
  containers:
  - name: my-app
    image: nginx
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
  - key: "spot"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
  - key: "maintenance"
    operator: "Exists"
    effect: "NoExecute"
    tolerationSeconds: 3600
```

## 🎯 Node Affinity

### Affinity Types
- **RequiredDuringSchedulingIgnoredDuringExecution**: Hard requirement
- **PreferredDuringSchedulingIgnoredDuringExecution**: Soft preference

### Affinity Examples
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  containers:
  - name: my-app
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
            - e2e-az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
```

## 🔗 Pod Affinity & Anti-Affinity

### Pod Affinity (Co-location)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
  labels:
    app: web
spec:
  containers:
  - name: web
    image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: web-client
spec:
  containers:
  - name: client
    image: busybox
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - web
        topologyKey: kubernetes.io/hostname
```

### Pod Anti-Affinity (Spread)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: nginx
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web
            topologyKey: kubernetes.io/hostname
```

## 🚀 Essential Commands

### Node Management
```bash
# Label node
kubectl label nodes node1 zone=us-west

# Remove label
kubectl label nodes node1 zone-

# View node labels
kubectl get nodes --show-labels

# View node details
kubectl describe node node1
```

### Pod Scheduling
```bash
# Check pod scheduling events
kubectl describe pod my-pod | grep -A 10 Events

# Check node capacity
kubectl describe nodes | grep -A 5 "Allocated resources"

# Force pod scheduling (bypass taints)
kubectl create pod my-pod --overrides='{"spec":{"tolerations":[{"key":"dedicated","operator":"Equal","value":"gpu","effect":"NoSchedule"}]}}'
```

## 💡 Pro Tips

- **Use taints** for dedicated workloads (GPU, high-memory)
- **Use affinity** for performance optimization
- **Use anti-affinity** for high availability
- **Test scheduling** before production
- **Monitor pod placement** with `kubectl get pods -o wide`
- **Use node selectors** for simple requirements

## 🔍 Troubleshooting

### Scheduling Issues
```bash
# Check why pod is pending
kubectl describe pod pending-pod

# Check node taints
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints

# Check node capacity
kubectl describe nodes | grep -A 10 "Allocated resources"

# Check pod events
kubectl get events --sort-by='.lastTimestamp' | grep pending-pod
```

### Affinity Issues
```bash
# Check node labels
kubectl get nodes --show-labels

# Check pod affinity rules
kubectl get pod my-pod -o yaml | grep -A 20 affinity

# Test node selector
kubectl get nodes -l zone=us-west
```

## 📊 Quick Reference

| Feature | Purpose | Use Case |
|---------|---------|----------|
| **Taint** | Repel pods from nodes | Dedicated workloads |
| **Toleration** | Allow pods on tainted nodes | GPU workloads |
| **Node Affinity** | Attract pods to nodes | Performance optimization |
| **Pod Affinity** | Co-locate pods | Cache co-location |
| **Pod Anti-Affinity** | Spread pods | High availability |

## 🚀 Advanced Examples

### GPU Node Setup
```bash
# Label GPU node
kubectl label nodes gpu-node accelerator=nvidia-tesla-k80

# Taint GPU node
kubectl taint nodes gpu-node nvidia.com/gpu=true:NoSchedule

# Deploy GPU workload
kubectl apply -f gpu-pod.yaml
```

### High Availability Setup
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ha-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ha-app
  template:
    metadata:
      labels:
        app: ha-app
    spec:
      containers:
      - name: app
        image: my-app:latest
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - ha-app
            topologyKey: kubernetes.io/zone
```

## 📝 Summary

- **Taint** = Repel pods from nodes
- **Toleration** = Allow pods on tainted nodes
- **Node Affinity** = Attract pods to specific nodes
- **Pod Affinity** = Co-locate pods together
- **Pod Anti-Affinity** = Spread pods apart

**Next**: Day 09 - Ingress & TLS 