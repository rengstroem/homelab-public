# SSL / TLS Strategy

## Two legs, both secured

```
User ──HTTPS──► Cloudflare ──HTTPS (Origin Cert)──► NGINX
```

**User → Cloudflare:** Cloudflare manages this with their own certificate. Nothing to do here.

**Cloudflare → NGINX:** Cloudflare Origin Certificate installed on the NGINX container. SSL mode is Full (Strict) — Cloudflare validates the cert before accepting the connection.

---

## Why Full (Strict) and not just Full

Full mode encrypts the Cloudflare → origin connection but accepts a self-signed cert. The connection is encrypted but the origin isn't authenticated — a MITM between Cloudflare and the container would not be detected.

Full (Strict) adds certificate validation. The Origin Certificate is free and takes 10 minutes to set up, so there's no reason to accept the weaker guarantee.

---

## Origin Certificate

| Property | Value |
|---|---|
| Type | Cloudflare Origin Certificate |
| Coverage | Apex + wildcard (`*.homelab.example.com`) |
| Validity | 15 years |
| Trusted by | Cloudflare only |

Not browser-trusted by design. Direct access to the origin IP without going through Cloudflare shows a certificate error. That's intentional — the origin IP isn't in DNS anyway, but this adds another layer of friction for anyone who finds it.

Let's Encrypt would also satisfy Full (Strict) but renewal automation on an internal container is unnecessary overhead when a 15-year cert exists.
