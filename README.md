# WARP Config Generator — Cloudflare Worker

A minimal one-Worker website that creates a fresh Cloudflare WARP WireGuard registration and immediately downloads `wgcf-profile.conf`.

## Important compatibility note

`wgcf` v2.3.0 (18 September 2026) changed to WARP API `v0a5641` and **pins an Android TLS fingerprint**. This Worker mirrors the current API version, Android headers, TLS 1.2, cipher choices, curves and signature algorithms using Cloudflare Workers' `node:tls` support. However, Workers does not expose the same low-level uTLS ClientHello controls as the official `wgcf` implementation.

That means this zero-cost Worker version must be tested after deployment against the live WARP API. If Cloudflare returns HTTP 403, the Worker will show a clear compatibility error. In that case the reliable version is the Docker/VPS build using the real `wgcf` binary.

## Deploy — simplest route

You need a free Cloudflare account and Node.js installed on your computer.

```bash
cd warp-config-cloudflare
npm install
npx wrangler login
npm run deploy
```

Wrangler prints a URL similar to:

```text
https://warp-config-generator.YOUR-SUBDOMAIN.workers.dev
```

Open it and test **Create & Download Config**.

## Add your own domain later

In Cloudflare Dashboard:

1. Workers & Pages
2. Open `warp-config-generator`
3. Settings / Domains & Routes
4. Add Custom Domain
5. Enter a hostname such as `warp.example.com`

Cloudflare handles DNS and HTTPS for the Worker custom domain.

## What the Worker does

- Shows a Terms checkbox before generation.
- Generates a fresh X25519/WireGuard key pair in memory.
- Registers a fresh WARP device using the current `wgcf` API shape.
- Uses the Fire TV Stick 4K Max 2nd Gen model/name from the original BAT file.
- Fetches the assigned WARP WireGuard addresses and peer.
- Generates the profile with MTU 1280 and `PersistentKeepalive = 25`.
- Sends the `.conf` file straight to the browser.
- Does not write generated private keys or profiles to application storage.
- Applies a lightweight 45-second per-IP cache throttle to reduce accidental repeated registrations.

## Test locally

The unit tests do not create real WARP registrations:

```bash
npm test
```

For a local Worker preview:

```bash
npm run dev
```

The real registration path can only be verified against Cloudflare's live WARP API.

## Non-affiliation

This project is unofficial and is not affiliated with, authorized by, or endorsed by Cloudflare. Customers should only create registrations in accordance with Cloudflare's applicable terms and usage limits.
