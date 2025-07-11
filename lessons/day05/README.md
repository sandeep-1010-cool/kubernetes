# Day 05: Namespaces & RBAC

## 🎯 What are Namespaces?

**Namespaces** provide logical isolation within a cluster - like folders for organizing resources.

## 📁 Namespace Basics

### Default Namespaces
- **default**: Default namespace for resources
- **kube-system**: System components (kubelet, etcd)
- **kube-public**: Public resources
- **kube-node-lease**: Node lease objects

### Namespace Benefits
- ✅ **Resource isolation** - prevent conflicts
- ✅ **Access control** - different permissions per namespace
- ✅ **Resource quotas** - limit resource usage
- ✅ **Team organization** - separate dev/staging/prod

## 🚀 Namespace Commands

```bash
# Create namespace
kubectl create namespace my-namespace

# View namespaces
kubectl get namespaces

# Set default namespace
kubectl config set-context --current --namespace=my-namespace

# View resources in namespace
kubectl get all -n my-namespace

# Delete namespace (deletes all resources)
kubectl delete namespace my-namespace
```

## 🔐 RBAC (Role-Based Access Control)

### RBAC Components
- **Role**: Permissions within a namespace
- **ClusterRole**: Permissions across entire cluster
- **RoleBinding**: Binds role to user/group
- **ClusterRoleBinding**: Binds cluster role to user/group

## 📋 RBAC YAML Examples

### Role (Namespace-scoped)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: my-namespace
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### ClusterRole (Cluster-scoped)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

### RoleBinding (Namespace-scoped)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: my-namespace
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### ClusterRoleBinding (Cluster-scoped)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: User
  name: admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

## 🚀 Quick RBAC Commands

```bash
# Create role
kubectl create role pod-reader --verb=get,list,watch --resource=pods

# Create cluster role
kubectl create clusterrole secret-reader --verb=get,list,watch --resource=secrets

# Create role binding
kubectl create rolebinding jane-read-pods --role=pod-reader --user=jane

# Create cluster role binding
kubectl create clusterrolebinding admin-read-secrets --clusterrole=secret-reader --user=admin

# View roles
kubectl get roles
kubectl get clusterroles

# View role bindings
kubectl get rolebindings
kubectl get clusterrolebindings
```

## 🔑 Common Roles

### Built-in ClusterRoles
- **cluster-admin**: Full access to cluster
- **admin**: Full access within namespace
- **edit**: Read/write access within namespace
- **view**: Read-only access within namespace

### Custom Role Examples
```yaml
# Developer role (can deploy apps)
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: developer
rules:
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: [""]
  resources: ["pods", "services", "configmaps"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

## 💡 Pro Tips

- **Use namespaces** to organize teams/projects
- **Principle of least privilege** - give minimum required access
- **Test permissions** before production
- **Use built-in roles** when possible
- **Review RBAC regularly** for security
- **Document custom roles** for team reference

## 🔍 Troubleshooting

```bash
# Check user permissions
kubectl auth can-i get pods --namespace=my-namespace

# Check specific permission
kubectl auth can-i create deployments --namespace=my-namespace

# View user's roles
kubectl get rolebindings --all-namespaces | grep username

# Check service account permissions
kubectl auth can-i --as=system:serviceaccount:default:my-sa get pods
```

## 📊 Quick Reference

| Resource | Scope | Purpose |
|----------|-------|---------|
| **Role** | Namespace | Permissions within namespace |
| **ClusterRole** | Cluster | Permissions across cluster |
| **RoleBinding** | Namespace | Binds role to user/group |
| **ClusterRoleBinding** | Cluster | Binds cluster role to user/group |

## 🚀 Essential Commands

```bash
# Create namespace with quota
kubectl create namespace my-namespace

# Create service account
kubectl create serviceaccount my-sa -n my-namespace

# Bind role to service account
kubectl create rolebinding my-sa-binding --role=developer --serviceaccount=my-namespace:my-sa

# Check permissions
kubectl auth can-i --list --namespace=my-namespace

# View all role bindings
kubectl get rolebindings --all-namespaces
```

## 🔐 Security Best Practices

- **Use service accounts** instead of user accounts for apps
- **Limit cluster-admin** access to few users
- **Use namespaces** for resource isolation
- **Regular audits** of RBAC policies
- **Document access patterns** for team reference

## 📝 Summary

- **Namespace** = Logical isolation (like folders)
- **Role** = Permissions within namespace
- **ClusterRole** = Permissions across cluster
- **RoleBinding** = Connects roles to users/groups

**Next**: Day 06 - Helm Basics 