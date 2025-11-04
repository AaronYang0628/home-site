+++
title = "ArgoCD"
+++


Aaron use <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/svg/argo-cd.svg" height="20" width="20" style="display: inline-block; vertical-align: upper;" /> [https://argo-cd.72602.online](https://argo-cd.72602.online) to manage all kubernetes resources in 72602

### Upgrade Argocd
```shell
helm upgrade --install argo-cd argo-cd \
  --namespace argocd \
  --create-namespace \
  --version 8.3.5 \
  --repo https://aaronyang0628.github.io/helm-chart-mirror/charts \
  --values /root/home-site/content/Ops/Argo/CD/argocd.values.yaml \
  --atomic
```

{{% resources title="Related **files**" pattern=".*\.(yaml)" /%}}

### Login Argocd
{{< tabs groupid="Login From" title="Login From" >}}
{{< tab title="🖥️ECS" >}}

{{% notice style="transparent" %}}
```shell
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
```
{{% /notice %}}

{{< /tab >}}
{{< tab title="🏠72602" >}}

{{% notice style="transparent" %}}
```shell
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
MASTER_IP=$(kubectl get nodes --selector=node-role.kubernetes.io/control-plane -o jsonpath='{$.items[0].status.addresses[?(@.type=="InternalIP")].address}')
argocd login --insecure --username admin $MASTER_IP:30443 --password $ARGOCD_PASS
```
{{% /notice %}}

{{< /tab >}}
{{< /tabs >}}


### Manage Basic Components
Instead of using argocd, home still need to use ingress to expose services, and cert-manager to manage certificates.

{{< tabs title="Install" >}}

{{< tab title="📝Cert-Manager" >}}
{{% notice style="transparent" %}}
{{% include file="cert-manager/install.md" %}}
{{% /notice %}}
And we need to create a Let's Encrypt Issuer
{{% notice style="transparent" %}}
{{% include file="cert-manager/letencrypt-issuer.md" %}}
{{% /notice %}}
{{< /tab >}}

{{< tab title="Ingress-Nginx" >}}
{{% notice style="transparent" %}}
{{% include file="ingress-nginx/install.md" %}}
{{% /notice %}}
{{< /tab >}}

{{< tab title="Reloader" >}}
{{% notice style="transparent" %}}
{{% include file="reloader/install.md" %}}
{{% /notice %}}
{{< /tab >}}

{{< /tabs >}}


### Manage User Info
1. retrieve user `readonly` token
user `readonly` is using for [homepage](https://home.72602.online) website to grab some data from kubernetes
```shell
argocd account list
argocd account generate-token --account readonly
```


### Manage GitHub Repo
1. generate github deploy key
```shell
ssh-keygen -t rsa -b 4096 -C "github-deploy-key" -f id_rsa_github -N ""
```

2. create kubernetes secret
```shell
kubectl create secret generic github-ssh-key \
  --from-file=id_rsa=./id_rsa_github \
  --from-file=id_rsa.pub=./id_rsa_github.pub \
  --from-file=known_hosts=<(ssh-keyscan github.com)
```