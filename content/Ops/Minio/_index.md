+++
title = "Minio"
+++

### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.

- Cert-manager has installed on argoCD and the clusterissuer has a named `letsencrypt`, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>


### Deployment
```
kubectl -n argocd apply -f - << EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: minio
spec:
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
  project: default
  source:
    repoURL: https://aaronyang0628.github.io/helm-chart-mirror/charts
    chart: minio
    targetRevision: 16.0.10
    helm:
      releaseName: minio
      values: |
        global:
          imageRegistry: "m.daocloud.io/docker.io"
          imagePullSecrets: []
          storageClass: ""
          security:
            allowInsecureImages: true
          compatibility:
            openshift:
              adaptSecurityContext: auto
        image:
          registry: m.daocloud.io/docker.io
          repository: bitnami/minio
        clientImage:
          registry: m.daocloud.io/docker.io
          repository: bitnami/minio-client
        mode: standalone
        defaultBuckets: ""
        auth:
          existingSecret: "minio-secret"
        statefulset:
          updateStrategy:
            type: RollingUpdate
          podManagementPolicy: Parallel
          replicaCount: 1
          zones: 1
          drivesPerNode: 1
        resourcesPreset: "micro"
        resources: 
          requests:
            memory: 512Mi
            cpu: 250m
          limits:
            memory: 1024Mi
            cpu: 512m
        ingress:
          enabled: true
          ingressClassName: "nginx"
          hostname: console.minio.72602.online
          path: /
          annotations:
            cert-manager.io/cluster-issuer: letsencrypt
            traefik.ingress.kubernetes.io/router.entrypoints: web,websecure
          tls: true
        apiIngress:
          enabled: true
          ingressClassName: "nginx"
          hostname: api.minio.72602.online
          path: /
          annotations: 
            cert-manager.io/cluster-issuer: letsencrypt
            traefik.ingress.kubernetes.io/router.entrypoints: web,websecure
          tls: true
        persistence:
          enabled: true
          storageClass: "local-path"
          mountPath: /bitnami/minio/data
          accessModes:
            - ReadWriteOnce
          size: 8Gi
  destination:
    server: https://kubernetes.default.svc
    namespace: storage
EOF
```