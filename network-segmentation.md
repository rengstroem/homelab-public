# Network Segmentation

## Why

A flat home network means a compromised device — smart TV with ancient firmware, misconfigured container, whatever — has a direct path to everything else. That's not an acceptable situation when you're running internet-facing services on the same hardware as management interfaces.

Four VLANs based on function and how much I trust what's on them:

**VLAN 20 — TV / No-trust**
Smart TVs and streaming sticks. They get internet access. That's it. No route to anything else. Consumer device firmware is generally not something you can trust, and isolating them costs nothing.

**VLAN 30 — RE-MGMT**
Proxmox and UniFi management. Restricted to trusted workstations. Service and DMZ VLANs can't reach this unless there's a specific reason.

**VLAN 50 — RE-SERV**
Where services actually run. Reachable from the DMZ proxy on specific ports, not from the no-trust VLAN, not from the internet directly.

**VLAN 80 — RE-DMZ**
Internet-facing ingress only. NGINX is the only thing here. If it gets compromised, it can talk to RE-SERV on the ports it proxies — that's the extent of the damage.

---

## Firewall policy

Default deny between all VLANs. Allow rules added explicitly where there's a documented need. Misconfiguration fails closed.

---

## Management access

NGINX in the DMZ is managed from RE-MGMT only. No SSH from the internet. Each layer assumes the one above it might be compromised.
