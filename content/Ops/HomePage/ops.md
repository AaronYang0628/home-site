+++
title = "HomePage Ops"
hidden = true
+++

### Client
[<i class="fa-solid fa-link"></i> homepage web page (https://home.72602.online)](http://47.110.67.161:3000)


Tips:
1. need `metrics-server`

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
