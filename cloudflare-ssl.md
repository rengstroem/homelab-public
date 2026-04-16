# Cloudflare SSL / TLS

## Overview

All public-facing services sit behind Cloudflare, which handles DNS and acts as a reverse proxy at the edge. This provides DDoS protection, hides the origin IP, and allows SSL to be managed cleanly in two legs.

---

## SSL mode: Full (Strict)

Cloudflare's SSL mode is set to **Full (Strict)**. This means:

- Cloudflare serves a valid certificate to end users (managed automatically by Cloudflare)
- Cloudflare validates the certificate on the origin (NGINX) before accepting the connection
- A self-signed cert on the origin is **not** accepted — a trusted origin certificate is required

This prevents a scenario where traffic between Cloudflare and the origin could be intercepted or spoofed.

---

## Origin Certificate

A **Cloudflare Origin Certificate** is installed on the NGINX container. This is a certificate issued by Cloudflare specifically for the Cloudflare → origin leg. It:

- Is trusted by Cloudflare (satisfies Full Strict mode)
- Is **not** trusted by browsers directly (intentional — direct access bypassing Cloudflare is not the intended path)
- Has a configurable validity period (set to maximum: 15 years)

The certificate covers the apex domain and a wildcard (`*.homelab.example.com`), so adding new subdomains requires no certificate changes.

---

## Certificate installation

The Origin Certificate and private key are stored on the NGINX container at:

```
/etc/ssl/certs/cloudflare-origin.pem
/etc/ssl/private/cloudflare-origin.key
```

All NGINX server blocks reference these paths.

---

## DNS

All subdomains are managed as Cloudflare **proxied** (orange cloud) A records pointing to the origin IP. This ensures:

- Origin IP is not exposed in DNS lookups
- Traffic always flows through Cloudflare's edge
- Cloudflare WAF and DDoS protection apply to all subdomains
