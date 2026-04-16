# Overview

## Why Proxmox

I wanted a hypervisor I could actually own — no licensing headaches, proper LXC support alongside KVM, and a web UI that doesn't get in the way. Most services here run as LXC containers rather than full VMs. Lower overhead, and for things like NGINX or Plex there's no reason to carry a full kernel.

The VLAN-aware bridge setup in Proxmox (vmbr0 with VLAN tagging) means containers can be placed directly on whatever segment they belong on without needing separate physical interfaces. Works well with the UniFi switch.

## Network design

The network is split into four VLANs based on what's running and how much I trust it. Default policy between VLANs is deny — anything that needs to talk across segments has an explicit firewall rule for it.

Public traffic comes in via Cloudflare, hits NGINX in the DMZ, and gets proxied through to whatever service it's headed for. Backend services never have ports open to the internet directly.

See [network-topology.md](network-topology.md) for the full breakdown.

## Containers vs VMs

LXC for anything that doesn't need its own kernel — NGINX, Plex, GoAccess, KVM reserved for things that genuinely need it. Keeps resource usage sensible and makes the inventory easier to reason about.

## What's next

- Centralised logging
- Tighten up and document the firewall ruleset
- Deploy a bunch more services
- Nextcloud
- Get Authentik to work