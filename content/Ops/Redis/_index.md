+++
title = "Redis"
tags = ["redis", "ecs"]
+++

Due to Resource Limitations, Redis is not installed at home, I put it on the ECS server, and use the following script to start redis image

### Prepare `redis-credentials`
```
kubectl get namespaces database > /dev/null 2>&1 || kubectl create namespace database
kubectl -n database create secret generic redis-credentials \
  --from-literal=redis-password=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)
```


### Start Redis Server AT ECS
```
#!/bin/bash

# 设置 Redis 密码（请修改为你自己的强密码）
REDIS_PASSWORD="xxxxxx"

# 创建数据目录
mkdir -p $(pwd)/redis/data

# 启动 Redis 容器（带资源限制）
docker run -d \
  --name redis \
  -p 30679:6379 \
  -v $(pwd)/redis/data:/data \
  --restart always \
  --memory="512m" \
  --memory-swap="512m" \
  --cpus="1.0" \
  m.daocloud.io/docker.io/library/redis:8.4-rc1-bookworm \
  redis-server --requirepass ${REDIS_PASSWORD} --appendonly yes --maxmemory 450mb --maxmemory-policy allkeys-lru

echo "Redis 容器已启动！"
echo "容器名称: redis"
echo "对外端口: 30679 (映射到容器内 6379)"
echo "密码: ${REDIS_PASSWORD}"
echo "数据目录: $(pwd)/redis/data"
echo "内存限制: 512MB"
echo "CPU 限制: 1.0 核"
echo ""
echo "连接配置:"
echo "  主机: 你的服务器IP"
echo "  端口: 30679"
echo "  密码: ${REDIS_PASSWORD}"
echo ""
echo "本地测试连接命令:"
echo "docker exec -it redis redis-cli -a ${REDIS_PASSWORD}"
echo "或使用外部工具连接: redis-cli -h localhost -p 30679 -a ${REDIS_PASSWORD}"
echo ""
echo "查看资源使用:"
echo "docker stats redis"
```

### But also use ingress to expose redis service
```
kubectl -n storage apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: redis-server-service
spec:
  type: ClusterIP
  ports:
  - port: 6379
    targetPort: 30679
    protocol: TCP
    name: http
---
kubectl -n storage apply -f - <<EOF
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: redis-server-service
subsets:
  - addresses:
    - ip: "47.110.67.161"
    ports:
    - port: 30679
      protocol: TCP
      name: http
---
kubectl -n storage apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: redis-server-ingress
  annotations:
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "300"
spec:
  ingressClassName: nginx
  rules:
  - host: redis.72602.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: redis-server-service
            port:
              number: 6379
EOF
```