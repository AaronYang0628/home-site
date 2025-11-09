+++
title = "Redis"
+++

### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.


### Prepare `redis-credentials`
```
kubectl -n application create secret generic redis-credentials \
  --from-literal=redis-password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)
```

### Deployment
```
kubectl -n argocd apply -f - << 'EOF'
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: redis
spec:
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
  project: default
  source:
    repoURL: https://charts.bitnami.com/bitnami
    chart: redis
    targetRevision: 18.16.0
    helm:
      releaseName: redis
      values: |
        architecture: replication
        auth:
          enabled: true
          sentinel: false
          existingSecret: redis-credentials
        master:
          count: 1
          resources:
            requests:
              memory: 512Mi
              cpu: 512m
            limits:
              memory: 1024Mi
              cpu: 1024m
          disableCommands:
            - FLUSHDB
            - FLUSHALL
          persistence:
            enabled: true
            storageClass: "local-path"
            accessModes:
            - ReadWriteOnce
            size: 8Gi
        replica:
          replicaCount: 1
          resources:
            requests:
              memory: 512Mi
              cpu: 512m
            limits:
              memory: 1024Mi
              cpu: 1024m
          disableCommands:
            - FLUSHDB
            - FLUSHALL
          persistence:
            enabled: true
            storageClass: "local-path"
            accessModes:
            - ReadWriteOnce
            size: 8Gi
        image:
          registry: m.daocloud.io/docker.io
          pullPolicy: IfNotPresent
        sentinel:
          enabled: false
        metrics:
          enabled: false
        volumePermissions:
          enabled: false
        sysctl:
          enabled: false
        extraDeploy:
        - apiVersion: traefik.io/v1alpha1
          kind: IngressRouteTCP
          metadata:
            name: redis-tcp
            namespace: storage
          spec:
            entryPoints:
              - redis
            routes:
            - match: HostSNI(`*`)
              services:
              - name: redis-master
                port: 6379
  destination:
    server: https://kubernetes.default.svc
    namespace: storage
EOF
```


### Usage
```
kubectl -n storage get secret redis-credentials -o jsonpath='{.data.redis-password}' | base64 -d
```


### Test
```
kubectl -n storage run test --rm -it --image=m.daocloud.io/docker.io/library/redis:7 -- \
  redis-cli -h redis-master -p 6379 -a uItmVGpX5PShHc8j ping
```