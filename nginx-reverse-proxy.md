# NGINX Reverse Proxy

## Overview

NGINX runs in an LXC container on the RE-DMZ VLAN (VLAN 80). It is the single ingress point for all public-facing services — no backend services are directly exposed to the internet.

All traffic arrives via Cloudflare (proxied), meaning the public IP belongs to Cloudflare's edge network. NGINX receives connections from Cloudflare and forwards them to the appropriate backend container on RE-SERV (VLAN 50).

---

## Container placement

| Property | Value |
|---|---|
| CT ID | 101 |
| VLAN | RE-DMZ (VLAN 80) |
| Role | Reverse proxy / SSL termination point |

---

## SSL

SSL is handled in two legs:

1. **Client → Cloudflare:** Standard HTTPS via Cloudflare's edge certificate (managed by Cloudflare)
2. **Cloudflare → NGINX:** HTTPS using a Cloudflare Origin Certificate installed on the NGINX container

Cloudflare SSL mode is set to **Full (Strict)**, which validates the origin certificate and prevents MITM between Cloudflare and the origin. See [cloudflare-ssl.md](cloudflare-ssl.md) for details.

---

## Virtual host pattern

Each service gets its own server block. A typical service configuration follows this pattern:

```nginx
server {
    listen 443 ssl;
    server_name <service>.homelab.example.com;

    ssl_certificate     /etc/ssl/certs/cloudflare-origin.pem;
    ssl_certificate_key /etc/ssl/private/cloudflare-origin.key;

    location / {
        proxy_pass http://<BACKEND_IP>:<PORT>;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## Access logging

NGINX access logs are parsed and visualised by GoAccess. See [goaccess-log-monitoring.md](goaccess-log-monitoring.md).

---

## Services currently proxied

| Subdomain | Backend | Notes |
|---|---|---|
| `plex.homelab.example.com` | Plex CT on RE-SERV | Public |
| `logs.homelab.example.com` | GoAccess on RE-SERV | Internal use |
