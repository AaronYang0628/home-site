+++
title = "HomePage Ops"
hidden = true
+++

### Client
[<i class="fa-solid fa-link"></i> homepage web page (https://home.72602.online)](http://47.110.67.161:3000)


Tips:
1. need `metrics-server`


{{< tabs groupid="a" >}}
{{% tab title="docker" %}}
```shell
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
{{% tab title="k8s manifests" %}}
```shell
kubectl get namespace monitor > /dev/null 2>&1 || kubectl create namespace monitor
```

```shell
kubectl apply -f /root/home-site/content/Ops/HomePage/manifests
```
{{% /tab %}}

{{% tab title="argocd" %}}
```shell
kubectl get namespace monitor > /dev/null 2>&1 || kubectl create namespace monitor
```

```shell
kubectl -n argocd apply -f content/Ops/HomePage/homepage.values.yaml
```
{{% /tab %}}

{{< /tabs >}}



```shell
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:30000:0.0.0.0:30000' -N -f
```