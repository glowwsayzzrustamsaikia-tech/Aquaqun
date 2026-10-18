# AQUAQUN — website diagnostics starter

A mobile-first, black-first website diagnostics interface and optional Cloudflare Worker. This implementation preserves the supplied static HTML architecture and adds clearer DNS query evidence, richer RDAP field display, working tool shortcuts, and more complete on-page documentation.

## What works in this build

- Responsive scanner UI, mobile navigation, documentation, about, privacy and terms views.
- Client-side domain validation for public DNS names; the Worker validates inputs again.
- DNS-over-HTTPS lookups for A, AAAA, CNAME, MX, NS, TXT, SOA and CAA through Google Public DNS.
- Best-effort RDAP lookup through rdap.org. Registry redirects are deliberately not followed.
- Per-record-type status, actual returned values, TTL where supplied, safe error states, JSON copy/export.
- No fake scan progress, fabricated findings, made-up security score, persistent scan history, or fake authentication.
- Reduced-motion support and responsive tables.

## Important limitations — do not present as a complete security scanner

This Worker does **not** fetch arbitrary user-supplied websites. A normal Worker `fetch()` to a hostname does not by itself guarantee the connection uses only an IP validated earlier. Safe arbitrary target fetching requires connection-time destination enforcement (or a hardened egress proxy), validation of all A/AAAA answers, rejection of mixed permitted/prohibited answers, redirect-by-redirect validation, strict time/size/rate limits and DNS-rebinding tests.

Therefore these features remain **not connected**: HTTPS/TLS certificate diagnostics, security-header inspection, HTTP status/redirect chain, availability checks, robots.txt and sitemap retrieval. Do not add an unrestricted proxy endpoint to this Worker.

RDAP is best-effort. The public aggregator may redirect to a registry endpoint; this build reports that limitation instead of following an unvalidated redirect. Some RDAP fields can be absent or redacted.

## Android-friendly deployment

1. Open Cloudflare Dashboard → Workers & Pages → Create Worker.
2. Paste the contents of `worker/worker.js` into the Worker editor and deploy.
3. Open `https://YOUR-WORKER-URL/api/health`. It should return JSON describing the limited capabilities.
4. In `index.html`, change `const API_BASE=(window.AQUAQUN_API_BASE||'').replace(/\/+$/,'');` to `const API_BASE='https://YOUR-WORKER-URL';` (replace with your real Worker URL).
5. Upload `index.html` to Cloudflare Pages or another static host. Keep `worker/worker.js`, `wrangler.toml`, this README and tests in your source repository for future updates.
6. Open your deployed site, scan `example.com`, and inspect the DNS and Domain information tabs.

No API keys are required in the frontend or Worker for these current provider calls.

## API

- `GET /api/health`
- `GET /api/dns?domain=example.com&types=A,AAAA,MX,NS,TXT,SOA,CAA,CNAME`
- `GET /api/rdap?domain=example.com`

The current Worker uses permissive CORS for simple static deployment. Before public launch, restrict CORS to your deployed site origin and configure Cloudflare rate limiting/WAF rules. Inspect actual hosting/Worker/upstream logs and provider retention before publishing privacy claims. Replace `contact@example.com` with a monitored contact address.

## Known gaps before public launch

- No authentication, database, saved scan history, admin panel or security score.
- No arbitrary URL fetches or live HTTP/TLS/header/robots/sitemap checks.
- No in-Worker distributed rate limiter; configure platform-level rules.
- No custom domain-specific RDAP bootstrap implementation beyond the public aggregator.
- Deployment, provider behavior and mobile browser tests must be verified on the actual hosted site.

This is a working starter for the supported DNS/RDAP scope, not a complete penetration-testing platform or guarantee of security.
