# Proxmox Inventory

## Containers (LXC)

| CT ID | Hostname | VLAN | IP | Role | Status |
|---|---|---|---|---|---|
| 101 | nginx-proxy | RE-DMZ (80) | `<DMZ_PROXY_IP>` | NGINX reverse proxy | Running |
| 106 | plex | RE-SERV (50) | `<SERV_PLEX_IP>` | Plex Media Server | Running |

> IP addresses are omitted from this public documentation. Each container is assigned a static IP within its VLAN subnet.

---

## Planned containers

| Hostname | VLAN | Role |
|---|---|---|
| jellyfin | RE-SERV (50) | Jellyfin media server |
| vpn-client | RE-SERV (50) | VPN-confined download client |
| sonarr / radarr | RE-SERV (50) | \*arr automation stack |

---

## Notes

- All containers run on Proxmox VE
- LXC is preferred over full VMs for services that don't require a separate kernel
- Each container is placed on a single VLAN matching its trust level
- NGINX (CT 101) is the only container with a public-facing role; all others are internal
