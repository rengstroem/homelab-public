# Plex

LXC container on RE-SERV. Accessible publicly via NGINX — no direct port exposure.

---

## Container

| Property | Value |
|---|---|
| CT ID | 106 |
| VLAN | RE-SERV (VLAN 50) |
| Public URL | `plex.homelab.example.com` |

---

## Access path

```
User → Cloudflare → NGINX (DMZ) → Plex (RE-SERV)
```

---


