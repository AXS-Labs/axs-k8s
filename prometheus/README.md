To install prometheus to the cluster

1. Create Basic Auth secret for the ingress
```
htpasswd -c auth prometheus
kubectl create secret generic prometheus-ingress-basic-auth --from-file=auth --namespace=monitoring
```

2. Install prometheus via helm
```
helm install stable/prometheus -f ./config.yaml --name prometheus --namespace monitoring
```
