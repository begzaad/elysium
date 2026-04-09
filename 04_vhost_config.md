# 4 · VHost & IP Redirect Configuration

## IP Redirect (Default Server Block)

Any HTTP request to the raw lab IP (`<LAB_IP>`) is intercepted by Nginx's `default_server` and 301-redirected to `http://elysium.futr`.

```nginx
# /etc/nginx/conf.d/00_default.conf
server {
    listen 8080 default_server;
    server_name _;
    return 301 http://elysium.futr$request_uri;
}
```

**Verification:**
```bash
curl -I http://<LAB_IP>/
# HTTP/1.1 301 Moved Permanently
# Location: http://elysium.futr/
```

## Virtual Host Configuration

Each subdomain has a dedicated `server {}` block. All proxy to internal Docker services on the `elysium_net` bridge network. External port 80 is only exposed on the Nginx container.

### Internal Port Map

| Subdomain            | Upstream Container | Internal Port |
| -------------------- | ------------------ | ------------- |
| elysium.futr         | `main`             | 8080          |
| blog.elysium.futr    | `blog`             | 80            |
| shop.elysium.futr    | `shop`             | 8080          |
| hr.elysium.futr      | `hr`               | 5000          |
| api.elysium.futr     | `api`              | 3000          |
| git.elysium.futr     | `gitea`            | 3000          |
| mail.elysium.futr    | `mail`             | 80            |
| support.elysium.futr | `support`          | 8080          |
| monitor.elysium.futr | `grafana`          | 3000          |
| dev.elysium.futr     | `adminer`          | 8080          |
| vpn.elysium.futr     | `vpn`              | 8080          |

### Sample VHost Block (blog)

```nginx
# /etc/nginx/conf.d/blog.conf
server {
    listen 8080;
    server_name blog.elysium.futr;
    client_max_body_size 50M;
    location / {
        proxy_pass http://blog:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

## `/etc/hosts` Setup

On the attacker machine, add the following to `/etc/hosts` (or run `add_hosts.sh`):

```
<LAB_IP>   elysium.futr
<LAB_IP>   blog.elysium.futr
<LAB_IP>   shop.elysium.futr
<LAB_IP>   hr.elysium.futr
<LAB_IP>   api.elysium.futr
<LAB_IP>   git.elysium.futr
<LAB_IP>   mail.elysium.futr
<LAB_IP>   support.elysium.futr
<LAB_IP>   monitor.elysium.futr
<LAB_IP>   dev.elysium.futr
<LAB_IP>   vpn.elysium.futr
```

## Docker Network Architecture

```
[Attacker] ──→ <LAB_IP>:80 ──→ [elysium_nginx]
                                          │
                              ┌───────────┼───────────────┐
                              ↓           ↓               ↓
                           [main]      [blog]           [shop]
                           [hr]        [api]            [gitea]
                           [mail]      [support]        [grafana]
                           [adminer]   [vpn]            [prometheus]
                              │
                    ┌─────────┴─────────┐
                    ↓                   ↓
                 [mysql]             [mongo]
                (shop, support,   (api service)
                 wordpress)
```

All containers communicate on `elysium_net` (internal bridge). Only Nginx exposes port 80 externally.
