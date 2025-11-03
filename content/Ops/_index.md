+++
title = "Ops"
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

1. check top 10 RAM comusing thread
```shell
ps aux --sort=-%mem | head -n 11
```

2. generate github deploy key
```shell
ssh-keygen -t rsa -b 4096 -C "github-deploy-key" -f id_rsa_github -N ""
```

```shell
kubectl create secret generic github-ssh-key \
  --from-file=id_rsa=./id_rsa_github \
  --from-file=id_rsa.pub=./id_rsa_github.pub \
  --from-file=known_hosts=<(ssh-keyscan github.com)
```

3. enable podman tcp socket
```shell

```