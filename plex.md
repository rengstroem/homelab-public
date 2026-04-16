# Plex Media Server

## Overview

Plex runs in an LXC container on the RE-SERV VLAN and is publicly accessible via the NGINX reverse proxy. It is not directly exposed to the internet.

---

## Container

| Property | Value |
|---|---|
| CT ID | 106 |
| VLAN | RE-SERV (VLAN 50) |
| Public URL | `plex.homelab.example.com` |

---

## Access pattern

```
User → Cloudflare → NGINX (DMZ) → Plex CT (RE-SERV)
```

NGINX proxies requests from `plex.homelab.example.com` to the Plex container on RE-SERV. The Plex container has no direct internet-facing ports.

---

## Notes

- Media storage is mounted into the container from the Proxmox host
- Plex is being evaluated alongside Jellyfin as a long-term media solution — see [jellyfin-arr-stack.md](jellyfin-arr-stack.md)
