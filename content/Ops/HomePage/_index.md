+++
title = "HomePage"
+++


### Web Page
[<i class="fa-solid fa-link"></i> homepage web page (https://home.72602.online)](https://home.72602.online)

### Preliminary
- Kubernetes has installed, if not check 🔗<a href="/ops/index.html" target="_blank">link</a> </p>

- ArgoCD has installed, if not check 🔗<a href="/ops/argo/cd/index.html" target="_blank">link</a> </p>

- Ingres has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>
    1. The K3s server needs port 6443 to be accessible by all nodes.
    2. If you wish to utilize the metrics server, all nodes must be accessible to each other on port 10250.

- Cert-manager has installed on argoCD and the clusterissuer has a named `letsencrypt`, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>

- Reloader has installed on argoCD, if not check 🔗<a href="/ops/argo/cd/index.html#manage-basic-components" target="_blank">link</a> </p>



### Deployment
{{< tabs  title="Depoly For" >}}
{{< tab title="Production" icon="fa-solid fa-rocket" >}}

{{% notice style="transparent" %}}
```
ARGOCD_PASS=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
argocd login --insecure --username admin argo-cd.72602.online --password $ARGOCD_PASS
kubectl -n argocd apply -f /root/home-site/content/Ops/HomePage/homepage.values.yaml
```
{{% /notice %}}

{{< /tab >}}
{{< tab title="Development" icon="fa-solid fa-flask-vial" >}}

  different ways to test homepage settings
  {{< tabs groupid="tabs-example-language" >}}
  {{% tab title="docker" %}}
  ```
  docker run -d \
    --name homepage \
    -e HOMEPAGE_ALLOWED_HOSTS=47.110.67.161:3000 \
    -e PUID=1000 \
    -e PGID=1000 \
    -p 3000:3000 \
    -v /root/.kube/config:/app/.kube/config:ro \
    -v /root/home-site/static/icons:/app/public/icons  \
    -v /root/home-site/content/Ops/HomePage/config:/app/config \
    -v /var/run/docker.sock:/var/run/docker.sock:ro \
    --restart unless-stopped \
    ghcr.io/gethomepage/homepage:v1.5.0
  ```
  {{% /tab %}}
  {{% tab title="manifests" %}}
  ```
  kubectl get namespace monitor > /dev/null 2>&1 || kubectl create namespace monitor
  kubectl -n monitor apply -f /root/home-site/content/Ops/HomePage/manifests
  ```
  you might need to forward port 30000 to your local machine
  ```
  ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:30000:0.0.0.0:30000' -N -f
  ```
  {{% /tab %}}
  {{< /tabs >}}

{{< /tab >}}
{{< /tabs >}}


### Reference
For more information about homepage, please refer to [<i class="fa-solid fa-link"></i>https://github.com/gethomepage/homepage](https://github.com/gethomepage/homepage)



