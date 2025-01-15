# Домашнее задание к занятию "`Кластеризация и балансировка нагрузки`" - `Фумелли Петр`


### Задание 1

![alt text](https://github.com/username/reponame/blob/branch/path/image.png)
![alt text](https://github.com/username/reponame/blob/branch/path/image.png)

```
global
 log /dev/log local0
 log /dev/log local1 notice
 chroot /var/lib/haproxy
 stats socket /run/haproxy/admin.sock mode 660 level admin
 stats timeout 30s
 user haproxy
 group haproxy
 daemon

 # Default SSL material locations
 ca-base /etc/ssl/certs
 crt-base /etc/ssl/private

 # See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
 log global
 mode http
 option httplog
 option dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
 errorfile 400 /etc/haproxy/errors/400.http
 errorfile 403 /etc/haproxy/errors/403.http
 errorfile 408 /etc/haproxy/errors/408.http
 errorfile 500 /etc/haproxy/errors/500.http
 errorfile 502 /etc/haproxy/errors/502.http
 errorfile 503 /etc/haproxy/errors/503.http
 errorfile 504 /etc/haproxy/errors/504.http


listen stats  # веб-страница со статистикой
        bind                    :9000
        mode                    http
        stats                   enable
        stats uri               /stats
        stats refresh           5s
        stats realm             Haproxy\ Statistics

frontend example  # секция фронтенд
        mode http
        bind *:80
        #default_backend web_servers
 acl ACL_example.com hdr(host) -i example.com
 use_backend web_servers if ACL_example.com

backend web_servers    # секция бэкенд
        mode http
        balance roundrobin
        option httpchk
        http-check send meth GET uri /index.html
        server s1 127.0.0.1:8001 check
        server s2 127.0.0.1:8002 check

listen web_tcp

  bind :1325

 server s1 127.0.0.1:8001 check inter 3s
 server s2 127.0.0.1:8002 check inter 3s

```


### Задание 2

![alt text](https://github.com/username/reponame/blob/branch/path/image.png)
![alt text](https://github.com/username/reponame/blob/branch/path/image.png)

```
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http_front
    bind *:80
    acl host_example_local hdr(host) -i example.local
    use_backend servers if host_example_local

backend servers
    balance roundrobin
    option httpchk
    server server1 127.0.0.1:8001 check weight 2
    server server2 127.0.0.1:8002 check weight 3
    server server3 127.0.0.1:8003 check weight 4

listen stats
    bind :9000
    stats enable
    stats uri /stats
    stats refresh 5s
```
