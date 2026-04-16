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

## Notes

Running in parallel with Jellyfin while that setup is being finished. Long-term the plan is to migrate fully to Jellyfin — no account requirement, no telemetry, fully open source. Plex is fine but those things add up as reasons to move.

Media is mounted into the container from the Proxmox host.
