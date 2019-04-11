Install prometheus to the cluster
--------------------
1. Create Basic Auth secret for the ingress
```
htpasswd -c auth prometheus
kubectl create secret generic prometheus-ingress-basic-auth --from-file=auth --namespace=monitoring
```

2. Install prometheus via helm
```
helm install stable/prometheus -f ./config.yaml --name prometheus --namespace monitoring
```


Install Node Exporter on self hosted server
---------------------------

1. Follow installation instructions from https://prometheus.io/docs/guides/node-exporter/
2. Create NGINX reverse proxy which expose the metrics endpoint
```
server {
    listen 80;
    server_name XXXXX.com;
    location / {
        proxy_pass http://localhost:9100;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        auth_basic "Server Metrics";
        auth_basic_user_file /etc/nginx/XXXX.htpasswd;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/lanzendorf.axs-labs.com-0001/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/lanzendorf.axs-labs.com-0001/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
```
3. Secure the endpoint with basic auth: https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/
4. Create a service for the node exporter binary: https://devopscube.com/monitor-linux-servers-prometheus-node-exporter/
