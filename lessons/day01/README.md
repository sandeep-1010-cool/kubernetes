# Day 01: Kubernetes Overview & Architecture

## 🎯 What is Kubernetes?

**Kubernetes (K8s)** is a container orchestration platform that automates deploying, scaling, and managing containerized applications.

## 🏗️ Core Architecture

### Master Node (Control Plane)
- **API Server**: Frontend for K8s cluster - handles all requests
- **etcd**: Database storing all cluster data (configs, state)
- **Scheduler**: Decides which node runs which pod
- **Controller Manager**: Ensures desired state matches actual state

### Worker Nodes
- **Kubelet**: Agent running on each node - manages pods
- **Kube-proxy**: Network proxy - handles pod networking
- **Container Runtime**: Docker/containerd - runs containers

## 🔑 Key Concepts

| Component | Purpose | Quick Example |
|-----------|---------|---------------|
| **Node** | Physical/virtual machine running K8s | Your laptop running minikube |
| **Pod** | Smallest deployable unit | One container or group of related containers |
| **Cluster** | Group of nodes working together | Your entire K8s environment |

## 🚀 Quick Start Commands

```bash
# Check cluster status
kubectl cluster-info

# View nodes
kubectl get nodes

# Check pods
kubectl get pods

# Get detailed info
kubectl describe node <node-name>
```

## 📊 Architecture Diagram

```
┌─────────────────┐    ┌─────────────────┐
│   Master Node   │    │  Worker Node 1  │
│                 │    │                 │
│ • API Server    │    │ • Kubelet       │
│ • etcd          │    │ • Kube-proxy    │
│ • Scheduler     │    │ • Pods          │
│ • Controller    │    │                 │
└─────────────────┘    └─────────────────┘
         │                       │
         └───────────────────────┘
                 Network
```

## 💡 Pro Tips

- **etcd** is the single source of truth - backup regularly
- **Kubelet** is the primary node agent - keep it healthy
- **API Server** is stateless - can run multiple instances
- **Scheduler** only assigns pods, doesn't run them

## 🔍 Troubleshooting

```bash
# Check if master components are running
kubectl get componentstatuses

# View cluster events
kubectl get events

# Check node resources
kubectl top nodes
```

## 📝 Summary

- **Master** = Brain (control plane)
- **Worker** = Muscle (runs workloads)
- **etcd** = Memory (stores everything)
- **Kubelet** = Hands (manages pods)

**Next**: Day 02 - Pods, ReplicaSets, and Deployments 