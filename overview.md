# Architecture Overview

## Philosophy

The homelab is designed around principles I apply professionally: defense in depth, least privilege, and explicit trust boundaries. The goal is not just to run services, but to run them in a way that would survive scrutiny — network segmentation that mirrors what you'd expect in a regulated environment, with traffic flows that are deliberate rather than accidental.

This means more upfront work, but it also means the infrastructure is actually useful as a learning environment for security and platform engineering concepts.

---

## Why Proxmox VE

Proxmox was chosen as the hypervisor for several reasons:

- **LXC container support** alongside KVM VMs — most services run as lightweight containers, keeping resource overhead low while maintaining strong isolation
- **Open source** with an active community and enterprise-grade feature set
- **VLAN-aware networking** via Linux bridges, making it straightforward to place containers directly on specific VLANs
- **Web UI + CLI** — manageable both interactively and via automation

---

## Network approach

All services are placed on dedicated VLANs based on their trust level and function. Inter-VLAN routing is handled at the firewall layer with explicit allow rules — there is no blanket allow between segments.

Public-facing traffic is proxied through NGINX in the DMZ VLAN. The NGINX container terminates connections and forwards internally, so backend services are never directly internet-reachable.

See [network-topology.md](network-topology.md) for the full VLAN breakdown.

---

## Service philosophy

- Services run as **LXC containers** where possible (lower overhead, still isolated)
- Each service gets its own container — no co-location of unrelated services
- Public exposure is always via the reverse proxy, never direct port forwarding
- SSL terminates at Cloudflare edge with an Origin Certificate securing the Cloudflare → NGINX leg

---

## Future direction

- Expand the Jellyfin + \*arr media stack with a VPN-confined download client
- Add centralised logging and alerting
- Formalise firewall rules documentation
