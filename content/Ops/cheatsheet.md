
1. check top 10 RAM comusing thread
```shell
ps aux --sort=-%mem | head -n 11
```

7. [Optional]() forward some ports through ssh tunnel
```shell
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:31080:0.0.0.0:31080' -N -f
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip) -L '*:32443:0.0.0.0:32443' -N -f
```