# ADR-002: Cloudflare Origin Certificate + Full (Strict) SSL

**Date:** 2024  
**Status:** Accepted

---

## Context

Services are behind Cloudflare. Cloudflare has three SSL modes for the edge → origin leg: Flexible, Full, Full (Strict). A certificate on the origin is needed for the stronger options.

---

## Decision

Cloudflare Origin Certificate on the NGINX container. SSL mode set to Full (Strict).

---

## Alternatives considered

**Flexible mode** — Cloudflare → origin is unencrypted. Not an option.

**Full mode with self-signed cert** — encrypts the connection but doesn't authenticate the origin. MITM between Cloudflare and NGINX would go undetected. Not good enough when Full (Strict) costs nothing extra.

**Let's Encrypt** — also satisfies Full (Strict). Valid option, but renewal automation on an internal container adds complexity for no gain when a 15-year Origin Certificate exists and Cloudflare is already the DNS provider.

---

## Consequences

End-to-end encrypted and authenticated. Cloudflare validates the origin cert before connecting. Origin IP is hidden behind Cloudflare-proxied DNS.

The Origin Certificate isn't browser-trusted, so direct access to the origin IP shows a cert error. That's intentional — it's not supposed to be accessed directly.

If Cloudflare is ever removed from the stack, the cert needs replacing with a publicly trusted one. Acceptable tradeoff.
