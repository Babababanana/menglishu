# NginX
NginX can be used as
- Web server
- Proxy (layer 4 proxy: using stream context; layer 7 proxy: using http context)
    - Load Balancing
    - Backend Routing
    - Caching

## Web Server
```shell script
http {
    server {
        listen 8080;
        root /path/to/project_root;
        location /images {
            root /path/to/project_root;
        }
        location ~ .jpg$ {
            return 403;
        }
    }
}
```

## Proxy
### Layer 7 Proxy
#### Proxy to single server
```shell script
http {
    server {
        listen 8080;
        root /path/to/project_root;
        location /images {
            root /path/to/project_root;
        }
        location ~ .jpg$ {
            return 403;
        }
    }
    server {
        listen 8888;
        location / {
            proxy_pass http://localhost:8080;
        }
    }
}
```

#### LB - proxy to multiple servers
- Round robin
    ```shell script
    http {
        # default lb algo: round robin, requests will go to below servers one by one
        upstream myapps {
            server 127.0.0.1:1111;
            server 127.0.0.1:2222;
            server 127.0.0.1:3333;
            server 127.0.0.1:4444;
        }
        server {
            listen 8888;
            location / {
                proxy_pass http://myapps;
            }
        }
    }
    ```

- IP hash
    ```shell script
    http {
        # sticky session: requests will go to the same server based on ip
        upstream myapps {
            ip_hash;
            server 127.0.0.1:1111;
            server 127.0.0.1:2222;
            server 127.0.0.1:3333;
            server 127.0.0.1:4444;
        }
        server {
            listen 8888;
            location / {
                proxy_pass http://myapps;
            }
        }
    }
    ```

- Split app
    ```shell script
    http {
        upstream myapps {
            server 127.0.0.1:1111;
            server 127.0.0.1:2222;
            server 127.0.0.1:3333;
            server 127.0.0.1:4444;
        }
        upstream myapps1 {
            server 127.0.0.1:1111;
            server 127.0.0.1:2222;
        }
        upstream myapps2 {
            server 127.0.0.1:3333;
            server 127.0.0.1:4444;
        }
        server {
            listen 8888;
            location / {
                proxy_pass http://myapps;
            }
            location /app1 {
                proxy_pass http://myapps1;
            }
            location /app2 {
                proxy_pass http://myapps2;
            }
            location /admin {
                return 403;
            }
        }
    }
    ```

### Layer 4 Proxy
- One tcp connection goes to one server (multiple http requests can use same tcp connection)
- Layer 4 proxy is not aware of locations
```shell script
stream {
    upstream myapps {
        server 127.0.0.1:1111;
        server 127.0.0.1:2222;
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
    }
    server {
        listen 8888;
        proxy_pass http://myapps;
    }
}
```

## Encryption
```shell script
http {
    upstream myapps {
        server 127.0.0.1:1111;
        server 127.0.0.1:2222;
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
    }
    upstream myapps1 {
        server 127.0.0.1:1111;
        server 127.0.0.1:2222;
    }
    upstream myapps2 {
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
    }
    server {
        listen 8888;
        # public port, https enabled, with http2 protocol
        listen 443 ssl http2:

        # public key
        ssl_certificate /path/to/fullchain.pem;
        # private key
        ssl_certificate_key /path/to/privkey.pem;
        
        # TLS version 1.3
        ssl_protocols TLSv1.3;

        location / {
            proxy_pass http://myapps;
        }
        location /app1 {
            proxy_pass http://myapps1;
        }
        location /app2 {
            proxy_pass http://myapps2;
        }
        location /admin {
            return 403;
        }
    }
}
```

# Reference
- https://www.youtube.com/watch?v=hcw-NjOh8r0
- https://github.com/hnasr/javascript_playground/tree/master/nginx
