# ☸️ Kubernetes Learning Series

This repository provides a structured, day-wise learning path to master **Kubernetes (K8s)** through hands-on examples, real-world use cases, and troubleshooting guidance.

---

## 📂 Repository Structure

```
├── lessons/                 # Daily Kubernetes lessons and labs
│   ├── day01/               # Kubernetes basics and architecture
│   ├── day02/               # Pods, ReplicaSets, Deployments
│   └── ...
├── docs/                   # Setup and troubleshooting
│   ├── setup.md            # Installing minikube, kind, kubectl
│   └── troubleshooting.md  # Debugging pods, nodes, and configurations
└── README.md               # Series overview and navigation
```

---

## 📘 What You'll Learn

| Day | Topic                         | Key Concepts Covered                              |
| --- | ----------------------------- | ------------------------------------------------- |
| 01  | K8s Overview & Architecture   | Nodes, Master, Worker, Kubelet, etcd              |
| 02  | Pods & Deployments            | Pod YAML, ReplicaSet, rolling updates             |
| 03  | Services & Networking         | ClusterIP, NodePort, LoadBalancer, DNS            |
| 04  | Volumes & ConfigMaps          | Persistent Volumes, ConfigMap, Secret             |
| 05  | Namespaces & RBAC             | Role, ClusterRole, RoleBinding                    |
| 06  | Helm Basics                   | Helm install/upgrade, charts, templates           |
| 07  | Monitoring & Logging          | Metrics-server, Prometheus, Grafana, kubectl logs |
| 08  | Taints, Tolerations, Affinity | Pod scheduling and placement                      |
| 09  | Ingress & TLS                 | Ingress Controller, HTTPS setup                   |
| 10+ | Advanced Topics               | Operators, CRDs, GitOps, Autoscaling              |

---

## 🧰 Requirements

* Basic Linux & Docker knowledge
* `kubectl` CLI installed
* Local cluster (Minikube, Kind) or Cloud K8s

---

## 📄 Documentation

* [**docs/setup.md**](docs/setup.md) – Installing Kubernetes locally or in cloud
* [**docs/troubleshooting.md**](docs/troubleshooting.md) – Common issues & commands

---

## 🤝 Contributing

1. Fork the repo
2. Create a branch: `git checkout -b feature/lesson-update`
3. Commit changes: `git commit -m "Add lesson X"`
4. Push: `git push origin feature/lesson-update`
5. Submit a PR

---

## 📌 Notes

This course is hands-on and modular. Start from any topic or follow daily progression.

Master Containers, Deploy at Scale! 🧠⚙️
