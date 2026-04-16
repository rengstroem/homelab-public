# Proxmox Inventory

## Running containers

| CT ID | Hostname | VLAN | Role |
|---|---|---|---|
| 101 | nginx-proxy | RE-DMZ (80) | NGINX reverse proxy — only thing in the DMZ |
| 106 | plex | RE-SERV (50) | Plex Media Server |
| 108 | Actualbudget | RE-SERV (50) | Budgeting application |
| 107 | Authentik | RE-SERV (50) | Identity provider |

IPs are static within each VLAN subnet but not listed here.

---

## Planned

| Hostname | VLAN | Role |
|---|---|---|
| vpn-client | RE-SERV (50) | VPN container |
| Nextcloud | RE-SERV (50) | Nextcloud... |

---

## Notes

One service per container. No co-locating unrelated things — it makes cleanup and troubleshooting annoying and the resource savings aren't worth it.

NGINX (CT 101) is the only container that needs to be in the DMZ. Everything else is on RE-SERV and only reachable via the proxy.
