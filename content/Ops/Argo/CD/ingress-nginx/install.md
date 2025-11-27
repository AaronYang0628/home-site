```
kubectl -n argocd apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ingress-nginx
spec:
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
  project: default
  source:
    repoURL: https://kubernetes.github.io/ingress-nginx
    chart: ingress-nginx
    targetRevision: 4.12.3
    helm:
      releaseName: ingress-nginx
      values: |
        controller:
          image:
            registry: m.daocloud.io/registry.k8s.io
          service:
            enabled: true
            type: NodePort
            nodePorts:
              http: 31080
              https: 32443
          resources:
            requests:
              cpu: 200m
              memory: 512Mi
          admissionWebhooks:
            enabled: false
            patch:
              enabled: false
              image:
                registry: m.daocloud.io/registry.k8s.io
        metrics:
          enabled: false
        defaultBackend:
          enabled: false
          image:
            registry: m.daocloud.io/registry.k8s.io
  destination:
    server: https://kubernetes.default.svc
    namespace: basic-components
EOF
```