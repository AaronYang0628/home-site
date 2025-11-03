+++
title = "Cluster"
+++


1. init cluster
```shell
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | \
  INSTALL_K3S_MIRROR=cn \
  sh -s - server \
  --cluster-init \
  --flannel-backend=vxlan \
  --system-default-registry="registry.cn-hangzhou.aliyuncs.com"
```

2. cp kubeconfig
```shell
mkdir -p $HOME/.kube
cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
```



4. uninstall cluster
```shell
/usr/local/bin/k3s-uninstall.sh
```