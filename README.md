# Elysium Lab — Corporate Pentesting Environment

Elysium Lab is a fully Dockerized, realistic corporate pentesting environment designed for authorized security training. It simulates a mid-sized tech company with 10 subdomains, a heterogeneous stack, and 15 intentionally embedded vulnerabilities across web, API, and infrastructure services.

## Highlights
- 10 subdomains routed through a single Nginx reverse proxy
- Mixed technology stack (PHP, WordPress, Flask, Node.js, Gitea, Grafana, Adminer, Roundcube, etc.)
- 15 exploitable vulnerabilities (SQLi, IDOR, SSRF/XXE, JWT alg:none, CSRF, RCE upload, default creds, info disclosure)
- Complete write-up set with per-vulnerability reports

## Intended Use
This lab is for authorized pentesting practice only. Run it in an isolated network environment and do not expose it publicly.

## Quick Start
```bash
docker compose up -d
