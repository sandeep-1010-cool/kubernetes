# 🤖 AI Tools for Kubernetes Development

## 1. Essential Cursor IDE Extensions
- Kubernetes (ms-kubernetes-tools.vscode-kubernetes-tools)
- YAML (redhat.vscode-yaml)
- Docker (ms-azuretools.vscode-docker)
- GitLens (eamodio.gitlens)
- Kubernetes Support (ipedrazas.kubernetes-snippets)

---

## 2. Cursor AI Features & Prompt Examples
- Real-time manifest generation
- YAML validation & formatting
- Multi-file context
- Security & RBAC suggestions

**Prompt Examples:**
- "Create a Kubernetes deployment for a Node.js app with resource limits"
- "Generate a service manifest for my application"
- "Add resource limits and requests to this deployment"
- "Create a proper RBAC configuration for this service account"
- "Why is this pod failing to start?"

---

## 3. CLI Tools Installation

### Chocolatey (Windows)
If you don’t have Chocolatey, run in Administrator PowerShell:
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### Install Tools
- **Linux/macOS:**
  ```bash
  # kubectl
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  chmod +x kubectl
  sudo mv kubectl /usr/local/bin/
  ```
- **Windows:**
  ```powershell
  choco install kubernetes-cli -y
  choco install lens -y
  choco install k9s -y
  choco install trivy -y
  ```

---

## 4. kubectl Quick Reference
```bash
kubectl get pods
kubectl get deployments
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl apply -f deployment.yaml
kubectl port-forward svc/<service-name> 8080:80
```

---

## 5. Best Practices
- Use Cursor AI for manifest generation and validation.
- Always review and test AI-generated code in a dev environment.
- Use resource limits and RBAC in all deployments.
