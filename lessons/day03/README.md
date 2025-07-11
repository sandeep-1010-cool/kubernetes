# Day 03: Services & Networking

## 🎯 What are Services?

**Services** provide stable networking for pods - they give pods a permanent IP and DNS name.

## 🌐 Service Types

### 1. ClusterIP (Default)
- **Internal access only** - within cluster
- **Stable IP** - doesn't change when pods restart
- **DNS name** - `service-name.namespace.svc.cluster.local`

### 2. NodePort
- **External access** via node IP + port
- **Port range**: 30000-32767
- **Access pattern**: `http://node-ip:nodeport`

### 3. LoadBalancer
- **Cloud provider** creates external load balancer
- **Public IP** assigned automatically
- **Most expensive** option

## 📋 Service YAML Examples

### ClusterIP Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - port: 80        # Service port
    targetPort: 8080 # Pod port
```

### NodePort Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-nodeport
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30001  # External port
```

### LoadBalancer Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-lb
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
```

## 🚀 Quick Service Commands

```bash
# Create service
kubectl apply -f service.yaml

# Expose deployment as service
kubectl expose deployment my-app --port=80 --target-port=8080

# View services
kubectl get services

# Get service details
kubectl describe service <service-name>

# Delete service
kubectl delete service <service-name>
```

## 🔍 DNS & Service Discovery

### Internal DNS
```bash
# Service DNS format
my-app-service.default.svc.cluster.local

# Short form (same namespace)
my-app-service

# Cross namespace
my-app-service.other-namespace
```

### DNS Commands
```bash
# Test DNS resolution
kubectl run test-dns --image=busybox --rm -it --restart=Never -- nslookup my-app-service

# Check service endpoints
kubectl get endpoints <service-name>
```

## 🌍 Network Access Patterns

| Service Type | Access Method | Use Case |
|--------------|---------------|----------|
| **ClusterIP** | Internal only | Backend services |
| **NodePort** | Node IP:Port | Development/testing |
| **LoadBalancer** | Public IP | Production web apps |

## 🔧 Port Configuration

### Port Types Explained
- **port**: Service port (what clients connect to)
- **targetPort**: Pod port (where traffic goes)
- **nodePort**: Node port (for NodePort services)

```yaml
ports:
- port: 80        # Service listens on 80
  targetPort: 8080 # Pod receives on 8080
  nodePort: 30001  # External access on 30001
```

## 🚀 Essential Commands

```bash
# Create service from deployment
kubectl expose deployment my-app --type=NodePort --port=80

# Scale service (affects underlying pods)
kubectl scale deployment my-app --replicas=5

# Port forward for testing
kubectl port-forward service/my-app 8080:80

# Check service endpoints
kubectl get endpoints my-app-service
```

## 💡 Pro Tips

- **Use ClusterIP** for internal services
- **Use NodePort** for development/testing
- **Use LoadBalancer** for production web apps
- **Set resource limits** on pods for better performance
- **Use labels consistently** for service selectors
- **Test DNS resolution** before deploying

## 🔍 Troubleshooting

```bash
# Check service endpoints
kubectl get endpoints <service-name>

# Test service connectivity
kubectl run test-curl --image=curlimages/curl --rm -it --restart=Never -- curl my-app-service

# Check service events
kubectl describe service <service-name>

# Verify pod labels match service selector
kubectl get pods --show-labels
```

## 📊 Quick Reference

| Command | Purpose |
|---------|---------|
| `kubectl get svc` | List all services |
| `kubectl describe svc <name>` | Service details |
| `kubectl get endpoints <name>` | Service endpoints |
| `kubectl port-forward svc/<name> 8080:80` | Port forward |

## 📝 Summary

- **ClusterIP** = Internal access (default)
- **NodePort** = External via node IP
- **LoadBalancer** = External via cloud LB
- **DNS** = Automatic service discovery

**Next**: Day 04 - Volumes & ConfigMaps 