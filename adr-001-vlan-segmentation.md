# ADR-001: VLAN-based Network Segmentation

**Date:** 2024  
**Status:** Accepted

---

## Context

A flat home network with all devices on a single broadcast domain provides no isolation between devices. A compromised device has direct network access to all others. This is an unacceptable risk model for an environment running internet-facing services alongside sensitive management interfaces and untrusted consumer devices.

---

## Decision

Implement VLAN-based segmentation across four segments:

- **VLAN 20** — Untrusted consumer devices (TVs, streaming hardware)
- **VLAN 30** — Management plane (Proxmox, UniFi)
- **VLAN 50** — Internal services
- **VLAN 80** — DMZ (public-facing ingress only)

Inter-VLAN routing is handled at the firewall with a default-deny policy. Access between segments requires an explicit allow rule.

---

## Alternatives considered

### Single flat network
Rejected. No isolation between devices. A compromised TV or misconfigured container has direct access to management interfaces.

### Two-zone model (internal / DMZ only)
Considered but rejected in favour of finer granularity. A two-zone model would still mix untrusted IoT devices with internal services, and management traffic with general service traffic.

---

## Consequences

**Positive:**
- Blast radius of any single compromise is bounded by VLAN membership
- Management interfaces are not reachable from service or untrusted segments
- Internet-facing services are isolated in DMZ; compromise of NGINX does not give direct access to internal services
- Mirrors enterprise network architecture, making it a useful learning environment

**Negative:**
- More complex initial setup (VLAN-aware switch and Proxmox bridge configuration required)
- Inter-VLAN rules must be maintained as new services are added

---

## Notes

The VLAN IDs (20, 30, 50, 80) were chosen with deliberate gaps to allow future segments without renumbering.
