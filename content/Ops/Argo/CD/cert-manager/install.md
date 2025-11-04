
```
kubectl -n argocd apply -f - << EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cert-manager
spec:
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
  project: default
  source:
    repoURL: https://charts.jetstack.io
    chart: cert-manager
    targetRevision: 1.19.1
    helm:
      releaseName: cert-manager
      values: |
        installCRDs: true
        image:
          repository: m.daocloud.io/quay.io/jetstack/cert-manager-controller
          tag: v1.19.1
        webhook:
          image:
            repository: m.daocloud.io/quay.io/jetstack/cert-manager-webhook
            tag: v1.19.1
        cainjector:
          image:
            repository: m.daocloud.io/quay.io/jetstack/cert-manager-cainjector
            tag: v1.19.1
        acmesolver:
          image:
            repository: m.daocloud.io/quay.io/jetstack/cert-manager-acmesolver
            tag: v1.19.1
        startupapicheck:
          image:
            repository: m.daocloud.io/quay.io/jetstack/cert-manager-startupapicheck
            tag: v1.19.1
        resources:
          requests:
            cpu: 256m
            memory: 512Mi
          limits:
            cpu: 1000m
            memory: 1024Mi
  destination:
    server: https://kubernetes.default.svc
    namespace: basic-components
EOF
```