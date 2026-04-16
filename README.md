# Homelab

Running on Proxmox VE with a VLAN-segmented UniFi network. Everything public-facing goes through an NGINX reverse proxy in a DMZ VLAN, with Cloudflare in front of that. Backend services sit on a separate internal VLAN and are not directly reachable from the internet.

This repo is documentation — architecture, service configs, and why things are set up the way they are. Automation scripts live in a separate private repo.

---

## Stack

| Layer | What |
|---|---|
| Hypervisor | Proxmox VE |
| Networking | UniFi, VLAN-segmented |
| Reverse proxy | NGINX (LXC) |
| Edge / DNS | Cloudflare |
| SSL | Cloudflare Origin Cert, Full (Strict) |
| Log monitoring | GoAccess ||

---

## Repo layout

```
architecture/    # Network topology, Proxmox inventory, overall design
services/        # Per-service setup and config notes
security/        # Segmentation rationale, TLS setup
decisions/       # ADRs — why things are the way they are
```
