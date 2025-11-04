```
kubectl -n basic-components apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: proxy-client-service
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
  name: proxy-client-service
subsets:
  - addresses:
    - ip: "172.19.65.60"
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
  name: proxy-client-ingress
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "300"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "300"
spec:
  ingressClassName: nginx
  rules:
  - host: client.proxy.72602.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: proxy-client-service
            port:
              number: 80
EOF
```