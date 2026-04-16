# Cloudflare SSL

All public services sit behind Cloudflare. It handles DNS, acts as a reverse proxy at the edge, and takes care of DDoS mitigation. The origin IP isn't in DNS.

---

## SSL mode: Full (Strict)

Traffic between Cloudflare and the origin (NGINX) is encrypted and the certificate is validated. This rules out MITM on the internal leg.

Full (non-strict) encrypts the connection but accepts a self-signed cert — meaning the origin isn't authenticated. Not good enough.

Flexible mode (Cloudflare → origin unencrypted) is not something I'd consider.

---

## Origin Certificate

Cloudflare issues origin certificates specifically for the Cloudflare → origin leg. It satisfies Full (Strict) but isn't trusted by browsers directly — which is fine, because direct access bypassing Cloudflare isn't the intended path.

Covers the apex domain and wildcard (`*.homelab.example.com`), so adding subdomains doesn't require touching the cert.

Validity is set to 15 years. Let's Encrypt renewal automation on an internal-only container is more hassle than it's worth when this exists.

Cert and key on the NGINX container:

```
/etc/ssl/certs/cloudflare-origin.pem
/etc/ssl/private/cloudflare-origin.key
```

---

## DNS

All public subdomains are proxied A records (orange cloud in Cloudflare). Origin IP stays out of DNS and traffic always goes through the edge.
