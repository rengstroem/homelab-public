# Jellyfin + \*arr Stack

> **Status: Planned / In progress**

## Overview

The goal is a fully self-hosted media automation stack built around Jellyfin as the media server and the \*arr suite for content management, with all download traffic confined inside a VPN client container.

---

## Planned components

| Service | Role |
|---|---|
| Jellyfin | Media server (open-source Plex alternative) |
| Sonarr | TV show monitoring and automation |
| Radarr | Movie monitoring and automation |
| Prowlarr | Indexer management for Sonarr/Radarr |
| Download client | Torrent/usenet client, VPN-confined |
| VPN client | Confines download traffic to VPN tunnel |

---

## Network design

All download traffic will be routed through a dedicated VPN client container. The download client container will have its default gateway set to the VPN container, ensuring that if the VPN drops, download traffic stops rather than leaking over the clearnet.

The \*arr services and Jellyfin itself do not need VPN — only the download client container is confined.

```
Sonarr/Radarr → Download client → VPN container → Internet
Jellyfin       → NGINX (DMZ)    → Cloudflare    → User
```

---

## Rationale for Jellyfin

Jellyfin is fully open-source with no account requirement or telemetry, making it preferable over Plex for a privacy-conscious homelab. Plex currently runs in parallel while the Jellyfin setup is completed.

---

## Container placement

All components will run as LXC containers on RE-SERV (VLAN 50). No component in this stack requires a DMZ placement — public access to Jellyfin will go via NGINX exactly as Plex does today.
