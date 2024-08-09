---
title: "Nginx"
date: 2024-08-09T11:40:37+02:00
draft: false
weight: 100
---

# Functionalities of Nginx 

## 1. Web Server

The primary function is to serve web pages to client's browser

## 2. Load Balancing (as proxy server)

If a web server cannot handle the amount of requests to serve a web page, Nginx can be used as a Load Balencer. A Nginx load balancer server will route the traffic to several Nginx web servers behing it. Some Load Balancing methods include:

- **Least Connections**: Routes traffic to the server with the fewest active connections in the group. 
- **Round Robin**: Distributes client request in a sequencial, cyclical manner to each server in the group.

## 3. Caching (as proxy server)

Feching data from remote servers or databases on each request results in slower response times. With a caching server we can do the heavy lifting once and store the response. Caching does the following:
- Cache responses from backend server for frequently accessed resources.
- Copies are stored temporarily to improve the performance.

## 4. Security (as proxy server)

### Only entry point

Exponsing all servers to the public internet is not a good idea for security reasons. Each publicly exposed server represents an entry point that attackers can target, so you need to secure each and every server.

We can use one of the Nginx server as the **only entrypoint** that is publicly available for the application  This way, we gain:

- Consolidated Security
- Centralized Access Control
- Minimized Exposure
- Centralized logging & Monitoring

### Encrypted Communications

Nginx can handle SSL/TLS encryption and decryption, and forwards the encrypted message to the other servers, which they decrypt be themselves.

- Accept encrypted traffic
- Deny non encrypted requests

## 5. Compression and segmentation (as proxy server)

- **Compression**: Compresses the response to reduce bandwidth usage and improve load times.
- **Segmentation**: Sending responses in chunchs. Breaks the file into smaller chunks (video streaming)

## 6. Kubernetes Ingress Controller

- An Ingress Controller is a specialized load balancer for managing ingress (incoming) traffic in Kubernetes.
- It handles the routing to the appropriate services based on rules defined in an Ingress resource.
- Acts inside the kubernetes cluster, so it is NOT publicly accessible.
- Cloud load balancers (AWS, Azure, GCP) handle the incoming traffic from the internet and forward requests to the Ingress controller.


# Nginx Configuration 

- The main config file is typically named '**nginx.conf**'
- Uses a custom syntax comprising: Directives and Blocks
- This sets up the server's behavior

## 1. Web Server

### http

```
server {
    listen 80;
    server_name example.com www.example.com;

    location / {
        root /var/www/example.com;
        index index.html index.htm;
    }
}
```

### https

```
server {
    listen 80;
    server_name example.com www.example.com;

    ## Redirect all HTTP requests to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ## SSL Configuration
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    ## Security Headers
    add__header Strict-Transport-Security "max-age=31536000; includeSubDomains” always;
    #..

    location / {
        root /var/www/example.com;
        index index.html index.htm;
    }
}
```

## 2. Load Balancing

```
http {
    upstream myapp1 {
        least_conn; // round-robin is default, so no need to add this line when using round-robin
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```

## 3. Cache

```
http {
    # ...
    proxy_cache_path /data/nginx/cache keys_zone=mycache:10m;
    server {
        proxy_cache mycache;
        location / {
            proxy_pass http://localhost:8000;
        }
    }
}
```
