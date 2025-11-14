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


### Preparation
```
kubectl get namespaces n8n > /dev/null 2>&1 || kubectl create namespace n8n
kubectl -n n8n create secret generic n8n-middleware-credential \
  --from-literal=postgres-password='3HwignC6NM13O8gw'
```


### Deployment
```
kubectl -n argocd apply -f /root/home-site/content/Ops/N8N/n8n.values.yaml
```
{{% resources title="Related **yaml**" style="primary" expanded="false" pattern=".*\.(yaml|yml)" /%}}

### Test

1. test postgresql performance
```
SELECT pid, now() - query_start as duration, query 
FROM pg_stat_activity
WHERE state = 'active'  
ORDER BY duration DESC
```

2. test redis performance
```
kubectl run -it --rm redis-test \
  --image=m.daocloud.io/docker.io/library/redis:alpine \
  --restart=Never \
  -- redis-cli -h 47.xxx -p 30679 -a uItmVGpX5PShHc8j ping
```
```
time kubectl run -it --rm redis-test \
  --image=m.daocloud.io/docker.io/library/redis:alpine \
  --restart=Never \
  -- redis-cli -h 47.xxx -p 30679 -a uItmVGpX5PShHc8j --latency
```