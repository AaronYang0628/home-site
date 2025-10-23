+++
title = "Hugo Ops"
+++

1. 

```shell
kubectl get secret github-ssh-key -o json \
    | jq 'del(.metadata["namespace","creationTimestamp","resourceVersion","selfLink","uid"])' \
    | kubectl -n application apply -f -
```

```shell
kubectl -n argocd apply -f /root/home-site/content/Ops/Hugo/hugo.values.yaml
```

```shell
argocd app sync argocd/hugo-blog
```