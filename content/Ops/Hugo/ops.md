+++
title = "Hugo Ops"
+++

```shell
kubectl -n argocd apply -f /root/home-site/content/Ops/Hugo/hugo.values.yaml
```

```shell
argocd app sync argocd/hugo-blog
```