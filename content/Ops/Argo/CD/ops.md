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

3. install basic-components `cert-manager`
```shell
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

4. create  clusterIssuer
```shell
kubectl -n kube-system apply -f - << EOF
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    email: aaron19940628@gmail.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-account-key
    solvers:
    - http01:
        ingress:
          class: nginx
EOF
```

5. install basic-components ingress class
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

6. retrieve readonly token
```shell
argocd account list
argocd account generate-token --account readonly
```

7. [Optional]() forward some ports through ssh tunnel
```shell
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:31080:0.0.0.0:31080' -N -f
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:32443:0.0.0.0:32443' -N -f
```
