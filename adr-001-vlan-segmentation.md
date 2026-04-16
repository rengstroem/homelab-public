# ADR-001: VLAN-based Network Segmentation

**Date:** 2024  
**Status:** Accepted

---

## Context

Flat home network = every device can talk to every other device. That's fine if you trust everything on it. I don't — smart TVs have opaque software and poor update records, and running internet-facing services on the same L2 segment as management interfaces is asking for trouble.

---

## Decision

Four VLANs (20, 30, 50, 80) segmented by function and trust level. Default deny between segments. Explicit allow rules where cross-VLAN communication is needed.

---

## Alternatives considered

**Single flat network** — rejected. No isolation. A compromised device or container has a direct path to everything.

**Two-zone model (internal / DMZ)** — considered. Simpler, but mixes untrusted IoT with internal services and doesn't give the management plane its own segment. The extra complexity of four VLANs is worth it.

---

## Consequences

Gets the job done: compromising one segment doesn't give you the others. Management isn't reachable from service or DMZ VLANs. Untrusted devices are completely isolated.

Downside is that inter-VLAN rules need to be maintained as things change. Worth it.

VLAN IDs have deliberate gaps (20, 30, 50, 80) so new segments can be inserted without renumbering.
