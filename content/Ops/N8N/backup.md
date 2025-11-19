+++
title = "N8N Backup"
+++

https://docs.n8n.io/hosting/cli-commands/#uninstall-community-nodes-and-credentials

```
kubectl -n n8n exec -it deploy/n8n -- sh


n8n export:workflow --all --output=backups/workflows/

n8n export:credentials --all --output=backups/credentials/


n8n import:entities --inputDir ./outputs --truncateTables true
n8n import:workflow --separate --input=backups/workflows/
n8n import:credentials --separate --input=backups/credentials/
```