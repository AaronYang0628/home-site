```
kubectl -n basic-components apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: proxy-server-service
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 32080
    protocol: TCP
    name: http
EOF
```
```
kubectl -n basic-components apply -f - <<EOF
apiVersion: v1
kind: Endpoints
metadata:
  name: proxy-server-service
subsets:
  - addresses:
    - ip: "47.110.67.161"
    ports:
    - port: 32080
      protocol: TCP
      name: http
EOF
```
```
kubectl -n basic-components apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: proxy-server-ingress
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "300"
spec:
  ingressClassName: nginx
  rules:
  - host: server.proxy.72602.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: proxy-server-service
            port:
              number: 80
EOF
```