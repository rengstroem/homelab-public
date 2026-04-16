# Network Topology

## Setup

Proxmox host has a VLAN-aware Linux bridge (`vmbr0`) trunked to a UniFi managed switch. Containers get placed on their VLAN at the bridge level — no need for separate physical NICs per segment.

---

## VLANs

| VLAN | Name | Subnet | What lives here |
|---|---|---|---|
| 20 | TV / No-trust | `192.168.20.0/24` | Smart TVs, streaming sticks — gets internet, nothing else |
| 30 | RE-MGMT | `192.168.30.0/24` | Proxmox management, UniFi controller |
| 50 | RE-SERV | `192.168.50.0/24` | Internal services |
| 80 | RE-DMZ | `192.168.80.0/24` | Public-facing ingress — only NGINX lives here |

---

## Traffic flow

```
Internet
   │
Cloudflare  (DNS + edge proxy)
   │  HTTPS with Origin Certificate
NGINX  (CT 101, VLAN 80 — RE-DMZ)
   │  proxy_pass over internal network
Backend service  (VLAN 50 — RE-SERV)
```

VLAN 20 has internet access and no route to any other segment. The smart TV firmware situation is what it is — keeping it isolated is the practical response.

VLAN 30 (management) is not reachable from VLAN 50 or 80 except for specific cases where it has to be.

VLAN 80 (DMZ) is the only segment with internet-facing ports, and the only thing in it is the NGINX proxy. If that gets compromised it can talk to RE-SERV on the ports it needs for proxying — it can't roam freely.

---

## Inter-VLAN routing

Handled at the firewall. Default deny between all segments. Explicit rules where cross-VLAN communication is needed. This way misconfiguration fails closed.

---

## DNS

External DNS on Cloudflare. All public subdomains are proxied records (orange cloud) — origin IP doesn't appear in DNS.
