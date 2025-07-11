# Day 07: Monitoring & Logging

## 🎯 What is Monitoring & Logging?

**Monitoring** tracks cluster health and performance. **Logging** captures application and system events for debugging.

## 📊 Monitoring Stack

### 1. Metrics Server
- **Built-in metrics** - CPU, memory, network
- **Horizontal Pod Autoscaler** support
- **kubectl top** commands

### 2. Prometheus
- **Time-series database** for metrics
- **Pull-based** collection
- **Alerting** capabilities

### 3. Grafana
- **Visualization** dashboard
- **Query Prometheus** data
- **Custom dashboards**

## 🚀 Quick Setup Commands

### Metrics Server
```bash
# Enable metrics server (minikube)
minikube addons enable metrics-server

# Check if metrics server is running
kubectl get pods -n kube-system | grep metrics-server

# View node metrics
kubectl top nodes

# View pod metrics
kubectl top pods
```

### Prometheus & Grafana
```bash
# Add Prometheus Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Install Prometheus
helm install prometheus prometheus-community/kube-prometheus-stack

# Access Grafana (port forward)
kubectl port-forward svc/prometheus-grafana 3000:80

# Default credentials: admin/prom-operator
```

## 📈 Monitoring Commands

### Basic Metrics
```bash
# Node metrics
kubectl top nodes

# Pod metrics
kubectl top pods

# Pod metrics with labels
kubectl top pods --selector=app=my-app

# Container metrics
kubectl top pods --containers
```

### Resource Usage
```bash
# Check node capacity
kubectl describe nodes | grep -A 5 "Allocated resources"

# Check pod resource limits
kubectl describe pod my-pod | grep -A 10 "Limits"

# Get resource quotas
kubectl get resourcequota
```

## 📝 Logging Commands

### Basic Logging
```bash
# View pod logs
kubectl logs my-pod

# Follow logs (real-time)
kubectl logs -f my-pod

# View logs from specific container
kubectl logs my-pod -c my-container

# View logs from previous pod instance
kubectl logs my-pod --previous

# View logs with timestamps
kubectl logs my-pod --timestamps
```

### Advanced Logging
```bash
# View logs from multiple pods
kubectl logs -l app=my-app

# View logs from deployment
kubectl logs deployment/my-app

# View logs from last N lines
kubectl logs my-pod --tail=100

# View logs since specific time
kubectl logs my-pod --since=1h
```

## 🔧 Monitoring Configuration

### Pod Resource Monitoring
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: monitored-pod
spec:
  containers:
  - name: my-app
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
    - containerPort: 80
```

### Horizontal Pod Autoscaler
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

## 📊 Prometheus Configuration

### ServiceMonitor
```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app-monitor
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: my-app
  endpoints:
  - port: http
    interval: 30s
```

### PrometheusRule (Alerting)
```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: my-app-alerts
  namespace: monitoring
spec:
  groups:
  - name: my-app
    rules:
    - alert: HighCPUUsage
      expr: container_cpu_usage_seconds_total > 0.8
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High CPU usage detected"
```

## 💡 Pro Tips

- **Set resource limits** on all pods for better monitoring
- **Use structured logging** (JSON format)
- **Monitor application metrics** not just infrastructure
- **Set up alerts** for critical thresholds
- **Retain logs** for compliance and debugging
- **Use log aggregation** (ELK stack, Fluentd)

## 🔍 Troubleshooting

### Monitoring Issues
```bash
# Check metrics server status
kubectl get pods -n kube-system | grep metrics-server

# Check Prometheus targets
kubectl port-forward svc/prometheus-operated 9090:9090

# Check Grafana connectivity
kubectl port-forward svc/prometheus-grafana 3000:80
```

### Logging Issues
```bash
# Check if pod is generating logs
kubectl exec my-pod -- ls /var/log

# Check container logs directory
kubectl exec my-pod -- find /var/log -type f

# Test log generation
kubectl exec my-pod -- echo "test log entry"

# Check log rotation
kubectl exec my-pod -- ls -la /var/log/
```

## 📊 Quick Reference

| Tool | Purpose | Key Feature |
|------|---------|-------------|
| **Metrics Server** | Basic metrics | kubectl top |
| **Prometheus** | Time-series DB | Alerting |
| **Grafana** | Visualization | Dashboards |
| **kubectl logs** | Container logs | Real-time |

## 🚀 Essential Commands

```bash
# Monitor cluster health
kubectl get nodes -o wide
kubectl top nodes

# Monitor application health
kubectl get pods -o wide
kubectl top pods

# Check resource usage
kubectl describe nodes | grep -A 10 "Allocated resources"

# View application logs
kubectl logs deployment/my-app -f

# Monitor HPA
kubectl get hpa
kubectl describe hpa my-app-hpa
```

## 📈 Monitoring Best Practices

- **Monitor the 4 Golden Signals**: Latency, Traffic, Errors, Saturation
- **Set up dashboards** for different teams (Dev, Ops, Business)
- **Use alerting** but avoid alert fatigue
- **Monitor business metrics** not just technical metrics
- **Retain metrics** for capacity planning
- **Use log levels** appropriately (DEBUG, INFO, WARN, ERROR)

## 📝 Summary

- **Metrics Server** = Basic cluster metrics
- **Prometheus** = Time-series metrics DB
- **Grafana** = Visualization dashboards
- **kubectl logs** = Container log access

**Next**: Day 08 - Taints, Tolerations, Affinity 