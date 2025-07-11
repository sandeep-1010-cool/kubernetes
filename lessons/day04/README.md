# Day 04: Volumes & ConfigMaps

## 🎯 What are Volumes?

**Volumes** provide persistent storage for pods - data survives pod restarts and failures.

## 📦 Volume Types

### 1. EmptyDir
- **Temporary storage** - deleted when pod is deleted
- **Shared between containers** in same pod
- **Good for**: Caching, temporary files

### 2. PersistentVolume (PV)
- **Persistent storage** - survives pod lifecycle
- **Cluster-wide resource** - managed by admin
- **Storage classes** define provisioners

### 3. ConfigMap
- **Configuration data** - key-value pairs
- **Non-sensitive** information
- **Good for**: App configs, environment variables

### 4. Secret
- **Sensitive data** - encrypted at rest
- **Base64 encoded** values
- **Good for**: Passwords, API keys, certificates

## 📋 Volume YAML Examples

### EmptyDir Volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - name: cache-volume
      mountPath: /cache
  volumes:
  - name: cache-volume
    emptyDir: {}
```

### PersistentVolume & PersistentVolumeClaim
```yaml
# PersistentVolume (Admin creates)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data
---
# PersistentVolumeClaim (User requests)
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### ConfigMap
```yaml
# Create ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  database_url: "postgresql://localhost:5432/mydb"
  api_version: "v1"
  debug_mode: "true"
---
# Use in Pod
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-app
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: database_url
    volumeMounts:
    - name: config-volume
      mountPath: /config
  volumes:
  - name: config-volume
    configMap:
      name: my-config
```

### Secret
```yaml
# Create Secret
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded
  password: cGFzc3dvcmQ=
---
# Use in Pod
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-app
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
```

## 🚀 Quick Commands

### Volume Commands
```bash
# Create PVC
kubectl apply -f pvc.yaml

# View PVs and PVCs
kubectl get pv
kubectl get pvc

# Delete PVC (releases PV)
kubectl delete pvc my-pvc
```

### ConfigMap Commands
```bash
# Create ConfigMap from file
kubectl create configmap my-config --from-file=config.properties

# Create ConfigMap from literal
kubectl create configmap my-config --from-literal=key=value

# View ConfigMaps
kubectl get configmaps

# Get ConfigMap details
kubectl describe configmap my-config
```

### Secret Commands
```bash
# Create Secret from file
kubectl create secret generic my-secret --from-file=secret.properties

# Create Secret from literal
kubectl create secret generic my-secret --from-literal=username=admin

# View Secrets
kubectl get secrets

# Get Secret details (values are base64 encoded)
kubectl describe secret my-secret
```

## 🔧 Access Modes

### PV Access Modes
- **ReadWriteOnce (RWO)**: Single node read/write
- **ReadOnlyMany (ROM)**: Multiple nodes read-only
- **ReadWriteMany (RWM)**: Multiple nodes read/write

### Storage Classes
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
```

## 💡 Pro Tips

- **Use ConfigMaps** for non-sensitive configuration
- **Use Secrets** for sensitive data (passwords, keys)
- **Set resource limits** on PVCs to control costs
- **Use StorageClasses** for dynamic provisioning
- **Backup PV data** regularly
- **Test volume mounts** before production

## 🔍 Troubleshooting

```bash
# Check PVC status
kubectl describe pvc my-pvc

# Check PV status
kubectl describe pv my-pv

# Check pod volume mounts
kubectl describe pod my-pod

# Test ConfigMap access
kubectl exec -it my-pod -- env | grep CONFIG

# Decode Secret values
echo "YWRtaW4=" | base64 -d
```

## 📊 Quick Reference

| Resource | Purpose | Key Feature |
|----------|---------|-------------|
| **EmptyDir** | Temporary storage | Deleted with pod |
| **PV/PVC** | Persistent storage | Survives pod lifecycle |
| **ConfigMap** | Configuration data | Non-sensitive |
| **Secret** | Sensitive data | Base64 encoded |

## 🚀 Essential Commands

```bash
# Create storage class
kubectl apply -f storage-class.yaml

# Create PVC with storage class
kubectl apply -f pvc.yaml

# Mount volume in deployment
kubectl patch deployment my-app -p '{"spec":{"template":{"spec":{"volumes":[{"name":"data","persistentVolumeClaim":{"claimName":"my-pvc"}}]}}}}'

# Update ConfigMap
kubectl create configmap my-config --from-file=config.properties --dry-run=client -o yaml | kubectl apply -f -
```

## 📝 Summary

- **EmptyDir** = Temporary storage (ephemeral)
- **PV/PVC** = Persistent storage (survives pods)
- **ConfigMap** = Configuration (non-sensitive)
- **Secret** = Sensitive data (encrypted)

**Next**: Day 05 - Namespaces & RBAC 