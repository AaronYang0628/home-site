+++
title = "Hugo"
+++



### Web Page
[<i class="fa-solid fa-link"></i> port hugo page (https://port.72602.online)](https://port.72602.online)


### Deployment
{{< tabs  title="Depoly For" >}}
{{< tab title="Production" >}}

{{% notice style="transparent" %}}
```
kubectl get namespace application > /dev/null 2>&1 || kubectl create namespace application
```
```
kubectl get secret github-ssh-key -o json \
    | jq 'del(.metadata["namespace","creationTimestamp","resourceVersion","selfLink","uid"])' \
    | kubectl -n application apply -f -
```
```
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
```
```
kubectl -n argocd apply -f /root/home-site/content/Ops/Hugo/hugo.values.yaml
```
```
argocd app sync argocd/hugo-blog
```
{{% /notice %}}

{{< /tab >}}
{{< tab title="Development" >}}

  different ways to test homepage settings
  {{< tabs groupid="tabs-example-language" >}}
  {{% tab title="hugo" %}}
  ```
  cd /root/home-site/
  hugo server -D -p 9999
  ```
  {{% /tab %}}
  {{% tab title="manifests" %}}
  ```
  kubectl get namespace application > /dev/null 2>&1 || kubectl create namespace application
  ```
  ```
  kubectl -n application apply -f /root/home-site/manifests
  ```
  {{% /tab %}}
  {{< /tabs >}}

{{< /tab >}}
{{< /tabs >}}




