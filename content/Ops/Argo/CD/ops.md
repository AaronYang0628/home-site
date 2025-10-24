+++
title = "ArgoCD Ops"
hidden = true
+++

1. login from ECS
```shell
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
```

2. upgrade argocd helm chart
```shell
helm upgrade --install argo-cd argo-cd \
  --namespace argocd \
  --create-namespace \
  --version 8.3.5 \
  --repo https://aaronyang0628.github.io/helm-chart-mirror/charts \
  --values /root/home-site/content/Ops/Argo/CD/argocd.values.yaml \
  --atomic
```

3. install basic-components `ingress-nginx`
```shell
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
              tcp:
                8080: 31080
          resources:
            requests:
              cpu: 200m
              memory: 512Mi
          admissionWebhooks:
            enabled: true
            patch:
              enabled: true
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

4. install basic-components `cert-manager`
```shell
```

5. forward some ports through ssh tunnel
```shell
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:31080:0.0.0.0:31080' -N -f
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:32443:0.0.0.0:32443' -N -f
```
