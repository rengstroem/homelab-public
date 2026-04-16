# SSL/TLS Strategy

## Overview

SSL/TLS is handled in two distinct legs, both of which are secured. The choice of Cloudflare Full (Strict) mode is deliberate — it is the only mode that provides end-to-end encrypted and validated traffic between the user and the origin server.

---

## Two-leg model

```
User ──HTTPS──► Cloudflare Edge ──HTTPS (Origin Cert)──► NGINX Origin
     (Cloudflare-managed cert)        (Cloudflare Origin Certificate)
```

### Leg 1: User → Cloudflare
Cloudflare presents a certificate issued by a public CA (managed automatically). End users see a valid, browser-trusted certificate. No configuration needed on the origin for this leg.

### Leg 2: Cloudflare → NGINX
A **Cloudflare Origin Certificate** is installed on the NGINX container. Cloudflare validates this certificate before accepting the origin connection. This is what Full (Strict) mode enforces.

---

## Why Full (Strict) and not just Full?

Cloudflare's **Full** mode encrypts the Cloudflare → origin connection but does not validate the certificate — a self-signed cert is accepted. This means the connection is encrypted but not authenticated: a MITM between Cloudflare and the origin would not be detected.

**Full (Strict)** adds certificate validation, closing this gap. Since the Origin Certificate is free and straightforward to deploy, there is no reason to accept the weaker guarantee.

---

## Certificate details

| Property | Value |
|---|---|
| Type | Cloudflare Origin Certificate |
| Coverage | Apex domain + wildcard (`*.homelab.example.com`) |
| Validity | 15 years |
| Trusted by | Cloudflare only (not browsers directly) |
| Renewal | Manual, but 15-year validity makes this a non-issue for now |

---

## Direct access

Because the Origin Certificate is not trusted by browsers, direct access to the origin IP bypassing Cloudflare will show a certificate error. This is intentional — it discourages and makes visible any attempt to bypass the Cloudflare layer.

The origin IP is not published in DNS (all records are Cloudflare-proxied), so direct access requires knowing the IP out-of-band.
