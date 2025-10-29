+++
title = "Milvus RAG"
+++

[http://rag.demo.72602.online](http://rag.demo.72602.online)

### Setup
```shell
kubectl -n argocd  apply -f /root/home-site/content/Apps/RAG/rag.app.values.yaml
```

```shell
argocd app sync milvus-rag-app
```