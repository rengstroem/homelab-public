# Network Topology

## Physical setup

A VLAN-aware Linux bridge (`vmbr0`) is configured in Proxmox, connected to a UniFi managed switch. VLANs are trunked to the Proxmox host, allowing individual containers and VMs to be placed on specific segments without requiring separate physical interfaces.

---

## VLAN segments

| VLAN ID | Name | Subnet | Purpose |
|---|---|---|---|
| 20 | TV / No-trust | `192.168.20.0/24` | Smart TVs, streaming devices, untrusted IoT |
| 30 | RE-MGMT | `192.168.30.0/24` | Proxmox management, UniFi controller |
| 50 | RE-SERV | `192.168.50.0/24` | Internal services (Plex, etc.) |
| 80 | RE-DMZ | `192.168.80.0/24` | Public-facing services (NGINX reverse proxy) |

---

## Trust model

```
Internet
   │
   ▼
Cloudflare (edge, DDoS protection, DNS)
   │  HTTPS (Origin Certificate)
   ▼
NGINX (CT 101 — RE-DMZ, VLAN 80)
   │  Internal proxy_pass
   ▼
Backend services (RE-SERV, VLAN 50)
```

- **VLAN 20 (TV/No-trust):** Devices here have no route to management or service VLANs. Internet access only.
- **VLAN 30 (RE-MGMT):** Restricted to management traffic. Not reachable from untrusted VLANs.
- **VLAN 50 (RE-SERV):** Internal services. Reachable from the DMZ proxy but not directly from the internet.
- **VLAN 80 (RE-DMZ):** The only segment with ports reachable from the internet (via Cloudflare). Contains only the NGINX reverse proxy.

---

## Inter-VLAN routing

Inter-VLAN routing is handled at the firewall with explicit rules. Default policy between segments is **deny**. Allow rules are added only where there is a documented need (e.g. NGINX on VLAN 80 → specific service ports on VLAN 50).

---

## DNS

External DNS is managed through Cloudflare. Subdomains resolve to the Cloudflare-proxied IP; Cloudflare forwards to the NGINX container in the DMZ.
