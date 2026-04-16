# Homelab

A self-hosted infrastructure running on Proxmox VE, built around network segmentation, reverse proxying, and a growing stack of self-hosted services.

This repo documents architecture decisions, service configurations, and the reasoning behind design choices. Scripts and automation live in a separate private repo.

---

## Stack at a glance

| Layer | Technology |
|---|---|
| Hypervisor | Proxmox VE |
| Networking | UniFi (VLAN-segmented) |
| Reverse proxy | NGINX (LXC container) |
| DNS / edge | Cloudflare |
| SSL | Cloudflare Origin Certificate, Full (Strict) |
| Monitoring | GoAccess (NGINX log analysis) |
| Media | Plex, Jellyfin (planned) |
| Automation | \*arr stack + VPN-confined download client (planned) |

---

## Repository structure

```
homelab/
├── architecture/
│   ├── overview.md              # Design philosophy and goals
│   ├── network-topology.md      # VLAN segments and trust model
│   └── proxmox-inventory.md     # CT/VM roster with roles
├── services/
│   ├── nginx-reverse-proxy.md
│   ├── cloudflare-ssl.md
│   ├── plex.md
│   ├── jellyfin-arr-stack.md    # WIP / planned
│   └── goaccess-log-monitoring.md
├── security/
│   ├── network-segmentation.md
│   └── ssl-tls-strategy.md
└── decisions/
    ├── adr-001-vlan-segmentation.md
    └── adr-002-cloudflare-origin-cert.md
```

---

## Design goals

- **Segmentation by default** — services live in isolated VLANs with explicit inter-VLAN routing rules. Trust is earned, not assumed.
- **Minimal attack surface** — only necessary ports exposed; public-facing traffic terminates at Cloudflare.
- **Reproducibility** — configurations documented so rebuilding from scratch is fast.
- **Security as a first-class concern** — not bolted on after the fact.
