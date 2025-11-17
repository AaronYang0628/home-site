+++
title = "Clash"
+++


### Install
```
wget https://github.com/Dreamacro/clash/releases/download/v1.19.0/clash-linux-amd64-v1.19.0.gz
gzip -d clash-linux-amd64-v1.19.0.gz
chmod +x clash-linux-amd64-v1.19.0
mv clash-linux-amd64-v1.19.0 clash
```

### Configuration
```
vim .env
CLASH_URL ='https://2e09f299.ghelper.me/subs/clash/xxxxxxx'
CLASH_SECRET = 'xxxx'
```


### Startup

直接运行脚本文件`start.sh`

- 进入项目目录

```
$ cd clash-for-linux
```

- 运行启动脚本

```
$ sudo bash start.sh

正在检测订阅地址...
Clash订阅地址可访问！                                      [  OK  ]

正在下载Clash配置文件...
配置文件config.yaml下载成功！                              [  OK  ]

正在启动Clash服务...
服务启动成功！                                             [  OK  ]

Clash Dashboard 访问地址：http://<ip>:9090/ui
Secret：xxxxxxxxxxxxx

请执行以下命令加载环境变量: source /etc/profile.d/clash.sh

请执行以下命令开启系统代理: proxy_on

若要临时关闭系统代理，请执行: proxy_off

```

```
$ source /etc/profile.d/clash.sh
$ proxy_on
```

- 检查服务端口

```
$ netstat -tln | grep -E '9090|789.'
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN     
tcp6       0      0 :::7890                 :::*                    LISTEN     
tcp6       0      0 :::7891                 :::*                    LISTEN     
tcp6       0      0 :::7892                 :::*                    LISTEN
```

- 检查环境变量

```
$ env | grep -E 'http_proxy|https_proxy'
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
```

以上步鄹如果正常，说明服务clash程序启动成功，现在就可以体验高速下载github资源了。


```
HOST_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
echo "宿主机 IP: $HOST_IP"
```

```
kubectl create configmap proxy-config \
  --from-literal=http_proxy="http://<$HOST_IP>:7890" \
  --from-literal=https_proxy="http://172.19.65.60:7890" \
  --from-literal=no_proxy="localhost,127.0.0.1,10.0.0.0/8,192.168.0.0/16,172.16.0.0/12,.svc,.cluster.local"
```

### Usage 
```
# 4. 在 Deployment 中使用
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: your-app
  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
      - name: app
        image: your-image:tag
        envFrom:
        - configMapRef:
            name: proxy-config
        env:
        - name: HTTP_PROXY
          valueFrom:
            configMapKeyRef:
              name: proxy-config
              key: http_proxy
        - name: HTTPS_PROXY
          valueFrom:
            configMapKeyRef:
              name: proxy-config
              key: https_proxy
        - name: NO_PROXY
          valueFrom:
            configMapKeyRef:
              name: proxy-config
              key: no_proxy
EOF
```

### Ingress
```
kubectl -n basic-components apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: clash-ui-service
spec:
  type: ClusterIP
  ports:
  - port: 9090
    targetPort: 9090
    protocol: TCP
    name: http
EOF
```
```
kubectl -n basic-components apply -f - <<EOF
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: clash-ui-service-1
  labels:
    kubernetes.io/service-name: clash-ui-service
addressType: IPv4
ports:
- port: 9090
  protocol: TCP
  name: http
endpoints:
  - addresses:
      - "mini-pc"
    conditions:
      ready: true
EOF
```
```
kubectl -n basic-components apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: clash-ui-ingress
  annotations:
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "300"
spec:
  ingressClassName: nginx
  rules:
  - host: clash.72602.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: clash-ui-service
            port:
              number: 9090
EOF
```