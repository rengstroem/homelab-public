# Jellyfin + \*arr Stack

> **In progress**

---

## What this is

Jellyfin as the media server, Sonarr/Radarr/Prowlarr for automation, and a download client that routes all its traffic through a VPN container. Standard self-hosted media stack.

Replacing Plex — Jellyfin is fully open source, no Plex account needed, no telemetry.

---

## Components

| Service | Role |
|---|---|
| Jellyfin | Media server |
| Sonarr | TV monitoring and download automation |
| Radarr | Movie monitoring and download automation |
| Prowlarr | Indexer management for Sonarr and Radarr |
| Download client | Runs inside the VPN container's network namespace |
| VPN container | All download traffic exits through this |

---

## Network setup for downloads

The download client container has its default gateway set to the VPN container. If the VPN drops, the gateway disappears and traffic stops — it doesn't fall back to clearnet. Jellyfin and the \*arr services don't need VPN and are configured separately.

```
Sonarr / Radarr
      │
      ▼
Download client  →  VPN container  →  Internet
      
Jellyfin  →  NGINX (DMZ)  →  Cloudflare  →  User
```

---

## Container placement

Everything on RE-SERV (VLAN 50). Public access to Jellyfin goes via NGINX the same way Plex does today.
