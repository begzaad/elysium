# 2 · Domain & Subdomain Enumeration

## Primary Domain

| Domain         | IP Address | Notes              |
| -------------- | ---------- | ------------------ |
| `elysium.futr` | `<LAB_IP>` | Corporate homepage |

## Subdomain Discovery

Subdomains were discovered using virtual host fuzzing against the target IP, since the domain uses a non-public TLD (`.futr`). Standard subdomain bruteforce tools were used with SecLists wordlists.

### Methodology

```bash
# VHost fuzzing with ffuf
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -u http://<LAB_IP>/ \
     -H "Host: FUZZ.elysium.futr" \
     -fs <baseline_size>

# Alternative with gobuster
gobuster vhost -u http://<LAB_IP> \
               -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
               --append-domain elysium.futr
```

### Discovered Subdomains

| #   | Subdomain              | IP         | Technology                   | Service                    |
| --- | ---------------------- | ---------- | ---------------------------- | -------------------------- |
| 1   | `elysium.futr`         | `<LAB_IP>` | PHP / Apache                 | Corporate homepage         |
| 2   | `blog.elysium.futr`    | `<LAB_IP>` | WordPress 6.4 / PHP 8.1      | Company blog & news        |
| 3   | `shop.elysium.futr`    | `<LAB_IP>` | Custom PHP / MySQL           | Employee merchandise store |
| 4   | `hr.elysium.futr`      | `<LAB_IP>` | Python 3.11 / Flask / SQLite | Internal HR portal         |
| 5   | `api.elysium.futr`     | `<LAB_IP>` | Node.js / Express / MongoDB  | Internal REST API gateway  |
| 6   | `git.elysium.futr`     | `<LAB_IP>` | Gitea 1.21 (Go)              | Source code hosting        |
| 7   | `mail.elysium.futr`    | `<LAB_IP>` | Roundcube / Postfix / PHP    | Corporate webmail          |
| 8   | `support.elysium.futr` | `<LAB_IP>` | Custom PHP / MySQL           | IT help desk ticketing     |
| 9   | `monitor.elysium.futr` | `<LAB_IP>` | Grafana 10.2 OSS             | Infrastructure monitoring  |
| 10  | `dev.elysium.futr`     | `<LAB_IP>` | Adminer 4.8 / PHP            | Database admin panel       |
| 11  | `vpn.elysium.futr`     | `<LAB_IP>` | PHP / Apache                 | VPN configuration portal   |

### Technology Fingerprinting

Technologies were identified via:
- HTTP response headers (`Server`, `X-Powered-By`)
- Page source and meta tags
- Error messages and default pages
- `Wappalyzer` browser extension
- `whatweb` CLI tool

```bash
whatweb -a 3 http://blog.elysium.futr
whatweb -a 3 http://api.elysium.futr
```
