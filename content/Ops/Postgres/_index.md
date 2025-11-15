+++
title = "PostgreSQL"
tags = ["postgresql", "ecs"]
+++


### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.

- Cert-manager has installed on argoCD and the clusterissuer has a named `letsencrypt`, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>


### Prepare `postgresql-credentials`
```
kubectl get namespaces database > /dev/null 2>&1 || kubectl create namespace database
kubectl -n database create secret generic postgresql-credentials \
    --from-literal=postgres-password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16) \
    --from-literal=password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16) \
    --from-literal=replication-password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)

kubectl -n database create secret generic pgadmin-credentials \
    --from-literal=password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)
```

### Deployment
```
kubectl -n argocd apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: postgresql
spec:
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
  project: default
  source:
    repoURL: https://aaronyang0628.github.io/helm-chart-mirror/charts
    chart: postgresql
    targetRevision: 18.1.8
    helm:
      releaseName: postgresql
      values: |
        global:
          security:
            allowInsecureImages: true
        architecture: standalone
        auth:
          database: n8n
          username: n8n
          existingSecret: postgresql-credentials
        primary:
          resources:
            requests:
              cpu: 2
              memory: 512Mi
            limits:
              cpu: 3
              memory: 1024Mi
          persistence:
            enabled: true
            storageClass: local-path
            size: 8Gi
        readReplicas:
          replicaCount: 1
          persistence:
            enabled: true
            storageClass: local-path
            size: 8Gi
        backup:
          enabled: false
        image:
          registry: m.daocloud.io/registry-1.docker.io
          pullPolicy: IfNotPresent
        volumePermissions:
          enabled: false
          image:
            registry: m.daocloud.io/registry-1.docker.io
            pullPolicy: IfNotPresent
        metrics:
          enabled: false
          image:
            registry: m.daocloud.io/registry-1.docker.io
            pullPolicy: IfNotPresent
  destination:
    server: https://kubernetes.default.svc
    namespace: database
EOF
```


```
POSTGRES_PASSWORD=$(kubectl -n database get secret postgresql-credentials -o jsonpath='{.data.postgres-password}' | base64 -d)
podman run --rm \
    --env PGPASSWORD=${POSTGRES_PASSWORD} \
    --entrypoint psql \
    -it m.daocloud.io/docker.io/library/postgres:15.2-alpine3.17 \
    --host host.containers.internal \
    --port 32543 \
    --username postgres  \
    --dbname postgres  \
    --command 'SELECT datname FROM pg_database;
```

### Deploy pgadmin
```
kubectl -n argocd apply -f /root/home-site/content/Ops/Postgres/pgadmin.app.yaml
```