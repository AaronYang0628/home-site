+++
title = "Ops"
+++

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