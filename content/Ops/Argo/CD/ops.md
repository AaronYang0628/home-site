+++
title = "ArgoCD Ops"
hidden = true
+++

```shell
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
```

```shell
helm upgrade --install argo-cd argo-cd \
  --namespace argocd \
  --create-namespace \
  --version 8.3.5 \
  --repo https://aaronyang0628.github.io/helm-chart-mirror/charts \
  --values /root/home-site/content/Ops/Argo/CD/argocd.values.yaml \
  --atomic
```

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

```shell
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:30443:0.0.0.0:30443' -N -f
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:31080:0.0.0.0:31080' -N -f
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:32443:0.0.0.0:32443' -N -f
```

```shell
nohup hugo serve -p 80 --bind 0.0.0.0 > output.log 2>&1 &
```
