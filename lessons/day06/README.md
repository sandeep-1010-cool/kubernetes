# Day 06: Helm Basics

## 🎯 What is Helm?

**Helm** is the package manager for Kubernetes - like apt/yum for Linux, but for K8s applications.

## 📦 Helm Concepts

### Core Components
- **Chart**: Package containing K8s resources (like a zip file)
- **Release**: Instance of a chart running in cluster
- **Repository**: Collection of charts (like app store)
- **Tiller**: Helm server (deprecated in v3)

### Helm Benefits
- ✅ **Package applications** - bundle multiple K8s resources
- ✅ **Version management** - upgrade/downgrade easily
- ✅ **Templating** - customize deployments per environment
- ✅ **Rollback** - revert to previous versions
- ✅ **Dependencies** - manage complex applications

## 🚀 Quick Helm Commands

```bash
# Install Helm
curl https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz | tar xz
sudo mv linux-amd64/helm /usr/local/bin/

# Add repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update repositories
helm repo update

# Search charts
helm search repo nginx

# Install chart
helm install my-release bitnami/nginx

# List releases
helm list

# Uninstall release
helm uninstall my-release
```

## 📋 Chart Structure

```
my-chart/
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default values
├── charts/             # Dependencies
├── templates/          # K8s resource templates
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
└── README.md           # Documentation
```

### Chart.yaml Example
```yaml
apiVersion: v2
name: my-app
description: A Helm chart for my application
type: application
version: 0.1.0
appVersion: "1.0.0"
```

### values.yaml Example
```yaml
# Default values for my-app
replicaCount: 1

image:
  repository: nginx
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: {}
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
```

## 🔧 Template Examples

### Deployment Template
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-app.fullname" . }}
  labels:
    {{- include "my-app.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "my-app.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "my-app.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
```

### Service Template
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-app.fullname" . }}
  labels:
    {{- include "my-app.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    {{- include "my-app.selectorLabels" . | nindent 4 }}
```

## 🚀 Essential Commands

### Install/Upgrade
```bash
# Install with custom values
helm install my-app ./my-chart --values custom-values.yaml

# Upgrade existing release
helm upgrade my-app ./my-chart --values new-values.yaml

# Install with specific values
helm install my-app ./my-chart --set replicaCount=3 --set image.tag=v2.0.0

# Dry run (test without installing)
helm install my-app ./my-chart --dry-run --debug
```

### Manage Releases
```bash
# List all releases
helm list -A

# Get release status
helm status my-app

# Get release values
helm get values my-app

# Rollback to previous version
helm rollback my-app 1

# Delete release
helm uninstall my-app
```

## 💡 Pro Tips

- **Use semantic versioning** for chart versions
- **Test templates** with `--dry-run --debug`
- **Use values files** for different environments
- **Document dependencies** in Chart.yaml
- **Use hooks** for pre/post install actions
- **Validate charts** with `helm lint`

## 🔍 Troubleshooting

```bash
# Lint chart for errors
helm lint ./my-chart

# Test template rendering
helm template my-app ./my-chart

# Debug template values
helm template my-app ./my-chart --debug

# Check release history
helm history my-app

# View release manifest
helm get manifest my-app
```

## 📊 Quick Reference

| Command | Purpose |
|---------|---------|
| `helm install` | Install chart |
| `helm upgrade` | Update release |
| `helm uninstall` | Remove release |
| `helm list` | List releases |
| `helm rollback` | Revert to previous version |
| `helm template` | Render templates locally |

## 🔧 Advanced Features

### Hooks
```yaml
# Pre-install hook
apiVersion: batch/v1
kind: Job
metadata:
  name: "{{ .Release.Name }}-pre-install"
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-weight": "-5"
spec:
  template:
    spec:
      containers:
      - name: pre-install-job
        image: busybox
        command: ["echo", "Pre-install hook"]
      restartPolicy: Never
```

### Dependencies
```yaml
# Chart.yaml with dependencies
dependencies:
  - name: postgresql
    version: 12.x.x
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
```

## 📝 Summary

- **Chart** = Package of K8s resources
- **Release** = Running instance of chart
- **Template** = YAML with variables
- **Values** = Configuration for templates

**Next**: Day 07 - Monitoring & Logging 