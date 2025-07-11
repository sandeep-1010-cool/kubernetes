# Day 10+: Advanced Topics

## 🎯 What are Advanced Topics?

**Advanced Topics** cover production-ready Kubernetes features: Operators, CRDs, GitOps, and advanced autoscaling for enterprise workloads.

## 🤖 Kubernetes Operators

### What are Operators?
**Operators** are applications that use custom resources to manage applications and their components.

### Operator Benefits
- ✅ **Automated operations** - install, configure, upgrade
- ✅ **Application-specific logic** - domain knowledge
- ✅ **Self-healing** - automatic recovery
- ✅ **Day 2 operations** - backup, scaling, monitoring

### Popular Operators
- **Prometheus Operator** - monitoring stack
- **PostgreSQL Operator** - database management
- **Cert-Manager** - certificate management
- **Istio Operator** - service mesh

## 🚀 Quick Operator Commands

### Install Operator SDK
```bash
# Install operator-sdk
curl -L https://github.com/operator-framework/operator-sdk/releases/download/v1.28.0/operator-sdk_linux_amd64 -o operator-sdk
chmod +x operator-sdk
sudo mv operator-sdk /usr/local/bin/

# Create new operator
operator-sdk init my-operator --repo github.com/myuser/my-operator

# Add API
operator-sdk create api --group mygroup --version v1alpha1 --kind MyApp

# Build and deploy
make manifests
make install
make deploy
```

### Manage Operators
```bash
# List operators
kubectl get operators

# Check operator status
kubectl describe operator my-operator

# View operator logs
kubectl logs -n operators deployment/my-operator-controller-manager
```

## 📋 Custom Resource Definitions (CRDs)

### CRD Example
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: myapps.mygroup.example.com
spec:
  group: mygroup.example.com
  names:
    kind: MyApp
    listKind: MyAppList
    plural: myapps
    singular: myapp
  scope: Namespaced
  versions:
  - name: v1alpha1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              replicas:
                type: integer
                minimum: 1
                maximum: 10
              image:
                type: string
```

### Custom Resource Instance
```yaml
apiVersion: mygroup.example.com/v1alpha1
kind: MyApp
metadata:
  name: my-app-instance
spec:
  replicas: 3
  image: nginx:latest
  config:
    database_url: "postgresql://localhost:5432/mydb"
```

## 🔄 GitOps

### What is GitOps?
**GitOps** uses Git as the single source of truth for declarative infrastructure and applications.

### GitOps Principles
- ✅ **Declarative** - desired state in Git
- ✅ **Versioned** - complete audit trail
- ✅ **Automated** - automatic synchronization
- ✅ **Secure** - Git as source of truth

### Popular GitOps Tools
- **ArgoCD** - declarative GitOps for Kubernetes
- **Flux** - GitOps toolkit
- **Jenkins X** - CI/CD with GitOps

## 🚀 GitOps Setup

### Install ArgoCD
```bash
# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Port forward to UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### ArgoCD Application
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/myuser/my-app-manifests
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - CreateNamespace=true
```

## 📈 Advanced Autoscaling

### Vertical Pod Autoscaler (VPA)
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: '*'
      minAllowed:
        cpu: 100m
        memory: 50Mi
      maxAllowed:
        cpu: 1
        memory: 500Mi
```

### Cluster Autoscaler
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      containers:
      - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.21.0
        name: cluster-autoscaler
        command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/my-cluster
```

### Multi-Metric HPA
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: multi-metric-hpa
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
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  - type: Object
    object:
      metric:
        name: requests-per-second
      describedObject:
        apiVersion: networking.k8s.io/v1
        kind: Ingress
        name: my-app-ingress
      target:
        type: AverageValue
        averageValue: 10k
```

## 🚀 Essential Commands

### CRD Management
```bash
# Apply CRD
kubectl apply -f crd.yaml

# List CRDs
kubectl get crd

# Describe CRD
kubectl describe crd myapps.mygroup.example.com

# Delete CRD
kubectl delete crd myapps.mygroup.example.com
```

### GitOps Commands
```bash
# Install ArgoCD CLI
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

# Login to ArgoCD
argocd login localhost:8080

# Create application
argocd app create my-app --repo https://github.com/myuser/my-app-manifests --path k8s --dest-server https://kubernetes.default.svc --dest-namespace my-app

# Sync application
argocd app sync my-app

# Check application status
argocd app get my-app
```

### Autoscaling Commands
```bash
# Install VPA
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/vertical-pod-autoscaler/hack/vpa-up.sh

# Check VPA status
kubectl get vpa

# Install Cluster Autoscaler
kubectl apply -f cluster-autoscaler.yaml

# Check cluster autoscaler logs
kubectl logs -n kube-system deployment/cluster-autoscaler
```

## 💡 Pro Tips

- **Use operators** for complex applications (databases, monitoring)
- **Design CRDs carefully** - they're part of your API
- **Implement GitOps** for production environments
- **Monitor autoscaling** metrics and adjust thresholds
- **Test operators** in staging before production
- **Document custom resources** for team reference

## 🔍 Troubleshooting

### Operator Issues
```bash
# Check operator status
kubectl get operators
kubectl describe operator my-operator

# Check operator logs
kubectl logs -n operators deployment/my-operator-controller-manager

# Check custom resources
kubectl get myapp
kubectl describe myapp my-app-instance
```

### GitOps Issues
```bash
# Check ArgoCD application status
argocd app get my-app

# Check sync status
argocd app sync my-app --prune

# View application events
argocd app events my-app

# Check Git repository connectivity
argocd repo list
```

### Autoscaling Issues
```bash
# Check HPA status
kubectl get hpa
kubectl describe hpa my-app-hpa

# Check VPA status
kubectl get vpa
kubectl describe vpa my-app-vpa

# Check cluster autoscaler
kubectl get nodes
kubectl describe nodes | grep -A 5 "Allocated resources"
```

## 📊 Quick Reference

| Topic | Purpose | Key Feature |
|-------|---------|-------------|
| **Operators** | Application automation | Custom controllers |
| **CRDs** | Custom resources | Extend Kubernetes API |
| **GitOps** | Git as source of truth | Declarative deployments |
| **VPA** | Vertical scaling | Resource optimization |
| **Cluster Autoscaler** | Node scaling | Infrastructure automation |

## 🚀 Advanced Examples

### Custom Controller
```go
// controller.go
func (r *MyAppReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
    var myApp mygroupv1alpha1.MyApp
    if err := r.Get(ctx, req.NamespacedName, &myApp); err != nil {
        return ctrl.Result{}, client.IgnoreNotFound(err)
    }

    // Create deployment
    deployment := &appsv1.Deployment{}
    if err := r.Get(ctx, types.NamespacedName{Name: myApp.Name, Namespace: myApp.Namespace}, deployment); err != nil {
        if errors.IsNotFound(err) {
            deployment = r.createDeployment(&myApp)
            if err := r.Create(ctx, deployment); err != nil {
                return ctrl.Result{}, err
            }
        }
    }

    return ctrl.Result{}, nil
}
```

### GitOps Workflow
```yaml
# .github/workflows/gitops.yml
name: GitOps Sync
on:
  push:
    branches: [main]
jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Update kustomize
      run: |
        cd k8s
        kustomize edit set image my-app:${{ github.sha }}
    - name: Commit and push
      run: |
        git config user.name "GitHub Actions"
        git config user.email "actions@github.com"
        git add .
        git commit -m "Update image to ${{ github.sha }}"
        git push
```

## 📝 Summary

- **Operators** = Application automation with custom logic
- **CRDs** = Extend Kubernetes API with custom resources
- **GitOps** = Git as single source of truth
- **VPA** = Vertical pod autoscaling
- **Cluster Autoscaler** = Automatic node scaling

**Congratulations! You've completed the Kubernetes Learning Series! 🎉** 