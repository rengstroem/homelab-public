# GoAccess — NGINX Log Monitoring

## Overview

GoAccess provides real-time and historical analysis of NGINX access logs. It runs as a lightweight process that parses the NGINX log files and serves an HTML dashboard, accessible via the reverse proxy.

---

## Access

| Property | Value |
|---|---|
| URL | `logs.homelab.example.com` |
| Source logs | NGINX access logs from CT 101 |

---

## Setup pattern

GoAccess reads the NGINX access log and outputs a static HTML report (or a live WebSocket-based dashboard). The output is served by NGINX itself, so no additional service needs to be exposed.

A basic invocation for a static report:

```bash
goaccess /var/log/nginx/access.log \
  --log-format=COMBINED \
  -o /var/www/logs/index.html
```

For a live dashboard, GoAccess can run in `--real-time-html` mode with a WebSocket port, proxied through NGINX.

---

## NGINX configuration

The `logs.homelab.example.com` server block proxies to the GoAccess output. Access can be restricted at the NGINX level (IP allowlist or basic auth) since this exposes request metadata.

---

## Value

GoAccess gives immediate visibility into:

- Request volume and patterns per subdomain
- HTTP status code distribution
- Top IPs, user agents, and referrers
- Response time distribution

Useful for catching unexpected traffic, misconfigured clients, or early signs of scanning/probing activity.
