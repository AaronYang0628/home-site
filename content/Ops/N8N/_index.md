+++
title = "N8N"
+++

### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.

- Cert-manager has installed on argoCD and the clusterissuer has a named `letsencrypt`, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>




kubectl -n n8n create secret generic n8n-middleware-credential \
  --from-literal=postgres-password='CBRQQt4xVk4FOtHN' \
  --from-literal=redis-user='default' \
  --from-literal=redis-password='uItmVGpX5PShHc8j'


kubectl -n argocd apply -f /root/home-site/content/Ops/N8N/n8n.values.yaml

### Deployment
```
kubectl -n argocd apply -f - << EOF

EOF
```