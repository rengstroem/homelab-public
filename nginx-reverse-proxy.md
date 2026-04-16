# NGINX Reverse Proxy

LXC container on RE-DMZ (VLAN 80). Single ingress point for everything public-facing. Backend services don't have ports open to the internet — they only talk to NGINX.

---

## Container

| Property | Value |
|---|---|
| CT ID | 101 |
| VLAN | RE-DMZ (VLAN 80) |

---

## SSL

Two legs:

- **Client → Cloudflare:** Cloudflare handles this with their own managed cert. Nothing to configure.
- **Cloudflare → NGINX:** Cloudflare Origin Certificate installed on the container. Cloudflare SSL mode is Full (Strict), so it validates the cert before accepting the connection.

Details in [cloudflare-ssl.md](cloudflare-ssl.md).

---

## Virtual host pattern

Each service gets its own server block. Standard pattern:

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

## Currently proxied

| Subdomain | Backend |
|---|---|
| `plex.homelab.example.com` | Plex on RE-SERV |
| `logs.homelab.example.com` | GoAccess on RE-SERV |

---

## Logging

Access logs feed into GoAccess — see [goaccess-log-monitoring.md](goaccess-log-monitoring.md).
