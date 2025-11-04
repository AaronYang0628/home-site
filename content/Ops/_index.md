+++
title = "Ops"
description = "72602 Cluster Ops"
+++

### init k3s cluster
```
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
  INSTALL_K3S_MIRROR=cn \
  sh -s - server \
  --cluster-init \
  --flannel-backend=vxlan \
  --system-default-registry="registry.cn-hangzhou.aliyuncs.com"
```

### copy kubeconfig
```
mkdir -p $HOME/.kube
cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
```

### retrieve token
```
cat /var/lib/rancher/k3s/server/node-token
```

### uninstall cluster
```
/usr/local/bin/k3s-uninstall.sh
```

---






---
---

### Server CheatSheet
{{% include file="./cheatsheet.md" %}}