# ADR-002: Cloudflare Origin Certificate with Full (Strict) Mode

**Date:** 2024  
**Status:** Accepted

---

## Context

Services are published via Cloudflare as a reverse proxy. Cloudflare supports several SSL modes for the Cloudflare → origin leg: Flexible, Full, and Full (Strict). The choice of mode determines the security of the connection between Cloudflare's edge and the origin server.

A certificate is needed on the origin (NGINX container) to support the stronger modes.

---

## Decision

Use a **Cloudflare Origin Certificate** on the NGINX container and configure Cloudflare SSL mode to **Full (Strict)**.

---

## Alternatives considered

### Flexible mode
Cloudflare → origin connection is unencrypted. Rejected outright — any network path between Cloudflare and the origin would expose plaintext traffic.

### Full mode (with self-signed cert)
Encrypts the Cloudflare → origin leg but does not validate the certificate. A self-signed cert satisfies Full mode. Rejected because it provides no authentication of the origin — a MITM on the internal path would not be detected.

### Let's Encrypt certificate
A valid option and would also satisfy Full (Strict). Rejected in favour of the Origin Certificate because:
- Origin Certificate requires no ACME challenge or renewal automation (15-year validity)
- Origin Certificate is not browser-trusted, so direct-to-origin access is not silently accepted
- Simpler to deploy in an environment where Cloudflare is already the DNS provider

---

## Consequences

**Positive:**
- End-to-end encrypted and validated traffic from user to origin
- Origin IP is not exposed (Cloudflare proxies all traffic)
- Wildcard coverage means no certificate changes needed when adding subdomains
- Long validity period (15 years) eliminates renewal overhead

**Negative:**
- Origin Certificate is only trusted by Cloudflare — direct access to the origin shows a browser certificate error (accepted as intentional behaviour)
- If Cloudflare were removed from the stack, the certificate would need to be replaced with a publicly trusted one

---

## Notes

The intentional non-trust of the Origin Certificate by browsers is considered a feature: it makes visible any attempt to access the origin directly, and does not silently present a valid HTTPS connection to a client that has somehow obtained the origin IP.
