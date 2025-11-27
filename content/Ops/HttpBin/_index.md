+++
title = "Httpbin"
+++

+++
title = "HomePage"
+++


### Web Page
[<i class="fa-solid fa-link"></i> homepage web page (https://httpbin.72602.online/headers)](https://httpbin.72602.online/headers)

### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.

- Cert-manager has installed on argoCD and the clusterissuer has a named `letsencrypt`, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>



### Deployment
{{< tabs  title="Depoly For" >}}
{{< tab title="Production" icon="fa-solid fa-rocket" >}}

{{% notice style="transparent" %}}
```
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
kubectl -n argocd apply -f /root/home-site/content/Ops/HttpBin/httpbin.values.yaml
```
{{% /notice %}}

{{< /tab >}}
{{< tab title="Development" icon="fa-solid fa-flask-vial" >}}

  different ways to test homepage settings
  {{< tabs groupid="tabs-example-language" >}}
  {{% tab title="docker" %}}
  ```
  docker run -d \
    --name httpbin \
    --restart unless-stopped \
    m.daocloud.io/docker.io/kennethreitz/httpbin:latest
  ```
  {{% /tab %}}
  {{% tab title="manifests" %}}
  ```
  kubectl get namespace basic-components > /dev/null 2>&1 || kubectl create namespace basic-components
  kubectl -n basic-components apply -f /root/home-site/content/Ops/HttpBin/manifests
  ```
  {{% /tab %}}
  {{< /tabs >}}

{{< /tab >}}
{{< /tabs >}}

