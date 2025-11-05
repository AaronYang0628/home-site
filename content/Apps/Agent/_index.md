+++
title = "Basic Agent"
description = ""
+++


在线体验: [http://agent.demo.72602.online](http://agent.demo.72602.online)

```shell
kubectl -n argocd apply -f /root/home-site/content/Apps/Agent/agent.app.values.yaml
argocd app sync basic-agent-app
```