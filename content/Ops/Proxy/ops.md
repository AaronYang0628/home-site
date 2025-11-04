
+++
title = "PorxyAdmin Ops"
draft = true
+++

### Bridge +  Server
[<i class="fa-solid fa-link"></i> proxy-admin web page (http://server.proxy.72602.online)](http://server.proxy.72602.online)

### Client
[<i class="fa-solid fa-link"></i> proxy-client web page (http://client.proxy.72602.online)](http://client.proxy.72602.online)


### Install
{{% include file="ops/proxy/script/install.md" %}}


### Check proxy-admin status
```
systemctl status proxyadmin
```

### [[Optional]]() Create `server` endpoint & ingress
{{% include file="ops/proxy/server/install.md" %}}

### [[Optional]]() Create `client` endpoint & ingress
{{% include file="ops/proxy/client/install.md" %}}