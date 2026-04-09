# 03 — Services Overview

This section summarizes each exposed virtual host, its backing service, and its purpose in the lab.

## Edge Routing

- Reverse proxy: `nginx` (single external listener)
- External entrypoint: host TCP `80`
- Virtual host routing based on `Host` header to internal Docker services

## Service Catalog

| Virtual Host           | Backing Service | Stack                | Primary Purpose                           |
| ---------------------- | --------------- | -------------------- | ----------------------------------------- |
| `elysium.futr`         | `main`          | PHP + Apache         | Corporate marketing site                  |
| `blog.elysium.futr`    | `blog`          | WordPress            | News/blog platform with vulnerable plugin |
| `shop.elysium.futr`    | `shop`          | PHP + MySQL          | Employee/partner merchandise store        |
| `hr.elysium.futr`      | `hr`            | Flask + SQLite       | Internal HR portal                        |
| `api.elysium.futr`     | `api`           | Node.js + MongoDB    | Internal API gateway                      |
| `git.elysium.futr`     | `gitea`         | Gitea                | Internal source hosting                   |
| `mail.elysium.futr`    | `mail`          | Roundcube            | Internal webmail                          |
| `support.elysium.futr` | `support`       | PHP + MySQL          | IT support ticketing                      |
| `monitor.elysium.futr` | `grafana`       | Grafana + Prometheus | Monitoring dashboard                      |
| `dev.elysium.futr`     | `adminer`       | Adminer              | DB admin interface                        |
| `vpn.elysium.futr`     | `vpn`           | PHP + Apache         | VPN profile portal                        |

## Internal Data Services

- `mysql`: shared relational backend for shop/support/blog
- `mongo`: backend for API service
- `prometheus`: monitoring datasource for Grafana

## Runtime Notes

- Custom `main/shop/support/vpn` containers run as non-root (`www-data`) on internal port `8080`.
- Reverse-proxy container runs unprivileged and maps host `80` to internal `8080`.
- All services are isolated on the `elysium_net` Docker network.

