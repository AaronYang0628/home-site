+++
title = "Ops"
+++

Ops Section

```shell
ssh-keygen -t rsa -b 4096 -C "github-deploy-key" -f id_rsa_github -N ""
```

```shell
kubectl create secret generic github-ssh-key \
  --from-file=id_rsa=./id_rsa_github \
  --from-file=id_rsa.pub=./id_rsa_github.pub \
  --from-file=known_hosts=<(ssh-keyscan github.com)
```