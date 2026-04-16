# GoAccess — NGINX Log Monitoring

GoAccess parses NGINX access logs and outputs an HTML dashboard. Nothing fancy — it's just a fast way to see what's hitting the proxy without setting up a full logging stack.

---

## Access

`logs.homelab.example.com` — proxied through NGINX like everything else.

---

## Setup

GoAccess reads the NGINX access log and writes a static HTML report (or runs in real-time mode with a WebSocket connection). Static report generation:

```bash
goaccess /var/log/nginx/access.log \
  --log-format=COMBINED \
  -o /var/www/logs/index.html
```

For live mode, GoAccess runs with `--real-time-html` and exposes a WebSocket port that NGINX proxies.

---

## Why bother

It's useful for catching unexpected traffic patterns — scanners, misconfigured clients, unusual spikes on a specific subdomain. Takes 10 minutes to set up and gives you an immediate view of what's actually hitting the proxy.

The `logs` subdomain has restricted access at the NGINX level since it exposes request metadata.
