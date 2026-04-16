# Network Segmentation

## Rationale

Network segmentation is the most impactful single control in a homelab environment. Without it, a compromised device — a smart TV with outdated firmware, a misconfigured service container — has a direct path to everything else on the network. With it, the blast radius of any compromise is bounded by VLAN membership and explicit firewall rules.

The segmentation model here mirrors what you'd implement in a regulated enterprise environment: trust zones defined by function and risk level, with inter-zone traffic requiring explicit justification.

---

## Trust zones

### VLAN 20 — TV / No-trust
Devices in this segment are assumed to be untrustworthy: smart TVs, streaming sticks, and similar consumer devices with poor update track records and opaque software. They get internet access and nothing else. No route to any other VLAN.

### VLAN 30 — RE-MGMT
Management plane: Proxmox web UI, UniFi controller. Accessible only from trusted management workstations. Not reachable from service or DMZ VLANs except where explicitly required.

### VLAN 50 — RE-SERV
Internal services. Reachable from the DMZ proxy via specific proxy_pass rules, but not directly from the internet or from the no-trust VLAN. This is where application workloads live.

### VLAN 80 — RE-DMZ
The internet-facing zone. Contains only the NGINX reverse proxy. This container can accept inbound connections from the internet (via Cloudflare) and can proxy to RE-SERV — but only on specific ports defined by the proxy configuration.

---

## Firewall policy

Default inter-VLAN policy: **deny**.

Exceptions are added as explicit rules. This means:
- New services are isolated by default until routing is deliberately configured
- Misconfiguration fails closed, not open

---

## Defence in depth

Even within the DMZ, the NGINX container does not have direct SSH access from the internet — management is from RE-MGMT only. The principle is that each layer should assume the layer above it may be compromised.
