# Day 09: Ingress & TLS

## 🎯 What is Ingress?

**Ingress** manages external access to services - like a smart router that handles HTTP/HTTPS traffic and provides SSL termination.

## 🌐 Ingress Components

### Ingress Controller
- **Load balancer** for HTTP/HTTPS traffic
- **SSL termination** and certificate management
- **Path-based routing** and host-based routing
- **Rate limiting** and authentication

### Common Controllers
- **NGINX Ingress Controller** (most popular)
- **Traefik** (modern, feature-rich)
- **HAProxy** (high performance)
- **AWS ALB** (cloud-native)

## 🚀 Quick Setup Commands

### Install NGINX Ingress Controller
```bash
# Add Helm repo
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Install controller
helm install ingress-nginx ingress-nginx/ingress-nginx

# Check controller status
kubectl get pods -n ingress-nginx

# Get external IP
kubectl get svc -n ingress-nginx
```

### Install Traefik
```bash
# Add Helm repo
helm repo add traefik https://traefik.github.io/charts

# Install Traefik
helm install traefik traefik/traefik

# Check status
kubectl get pods -n default | grep traefik
```

## 📋 Ingress Examples

### Basic Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: basic-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

### Path-Based Routing
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      - path: /web(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### Multiple Hosts
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-host-ingress
spec:
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
  - host: web.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

## 🔒 TLS Configuration

### TLS Secret
```bash
# Create TLS secret
kubectl create secret tls my-tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key

# Create from file
kubectl create secret tls my-tls-secret \
  --from-file=tls.crt=path/to/cert.pem \
  --from-file=tls.key=path/to/key.pem
```

### Ingress with TLS
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: my-tls-secret
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

### Let's Encrypt (Cert-Manager)
```bash
# Install cert-manager
helm repo add jetstack https://charts.jetstack.io
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set installCRDs=true

# Create ClusterIssuer
kubectl apply -f cluster-issuer.yaml
```

### ClusterIssuer for Let's Encrypt
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: your-email@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```

### Ingress with Auto-TLS
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: auto-tls-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

## 🚀 Essential Commands

### Ingress Management
```bash
# Apply ingress
kubectl apply -f ingress.yaml

# View ingresses
kubectl get ingress

# Describe ingress
kubectl describe ingress my-ingress

# Delete ingress
kubectl delete ingress my-ingress
```

### TLS Management
```bash
# List TLS secrets
kubectl get secrets | grep tls

# View certificate details
kubectl describe secret my-tls-secret

# Check certificate expiration
kubectl get secret my-tls-secret -o jsonpath='{.data.tls\.crt}' | base64 -d | openssl x509 -text -noout
```

### Debugging
```bash
# Check ingress controller logs
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller

# Test ingress connectivity
curl -H "Host: myapp.example.com" http://INGRESS_IP

# Check ingress status
kubectl get ingress -o wide
```

## 💡 Pro Tips

- **Use path-based routing** for microservices
- **Enable SSL redirect** for security
- **Use Let's Encrypt** for free certificates
- **Set up monitoring** for ingress controller
- **Use annotations** for controller-specific features
- **Test TLS certificates** before production

## 🔍 Troubleshooting

### Common Issues
```bash
# Check ingress controller status
kubectl get pods -n ingress-nginx

# Check ingress events
kubectl describe ingress my-ingress

# Test service connectivity
kubectl port-forward svc/my-app-service 8080:80

# Check certificate status
kubectl get certificaterequests
kubectl get certificates
```

### Debug Commands
```bash
# Check ingress controller config
kubectl get configmap -n ingress-nginx nginx-configuration -o yaml

# View ingress controller logs
kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx

# Test TLS handshake
openssl s_client -connect myapp.example.com:443 -servername myapp.example.com
```

## 📊 Quick Reference

| Component | Purpose | Key Feature |
|-----------|---------|-------------|
| **Ingress Controller** | Load balancer | SSL termination |
| **Ingress Resource** | Routing rules | Path/host routing |
| **TLS Secret** | Certificate storage | SSL certificates |
| **Cert-Manager** | Auto certificate | Let's Encrypt |

## 🚀 Advanced Features

### Rate Limiting
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rate-limited-ingress
  annotations:
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

### Authentication
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: auth-ingress
  annotations:
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

## 📝 Summary

- **Ingress Controller** = HTTP/HTTPS load balancer
- **Ingress Resource** = Routing rules
- **TLS Secret** = SSL certificate storage
- **Cert-Manager** = Automatic certificate management

**Next**: Day 10 - Advanced Topics 