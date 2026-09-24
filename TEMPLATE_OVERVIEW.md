# Jitsi Meet — Self-Hosted Video Conferencing (5 services, TCP media via TURN)

One-click self-hosted [Jitsi Meet](https://jitsi.org) on Railway: web, prosody (XMPP), jicofo (focus), jvb (video bridge), and coturn (TURN server). Engineered for Railway's TCP-only public network — **3+ participant meetings work** because all media is relayed through your own coturn TURN server (TURN over TCP with per-user ephemeral credentials), then hops to the video bridge over Railway's private network.

All internal passwords and the TURN secret are generated automatically per deployment. Nothing to type at deploy time.

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.app/new?github_url=https://github.com/lNamelessl/jitsi-railway-template)

After deploying, create your moderator account (one shell command — see below), open your Railway domain, create a room, and share the link: registered users moderate, guests join freely.

# Deploy and Host

## About Hosting

This template provisions five services (~3.8 GB RAM total): **web** (`ghcr.io/jitsi/web`, pinned `stable-11248`) serves the meeting UI on your Railway domain with TLS terminated at Railway's edge; **prosody** runs the XMPP signaling (private network only); **jicofo** manages conferences; **jvb** bridges media; **coturn** relays media over TCP/3478 through a Railway TCP proxy (Railway assigns the public port — the stack references it automatically via `${{coturn.RAILWAY_TCP_PROXY_DOMAIN}}`/`${{coturn.RAILWAY_TCP_PROXY_PORT}}`). Volumes persist prosody accounts and generated config. Railway does not expose UDP publicly, so all media (including 1:1) transits coturn — budget for relay bandwidth (~$10–25/mo for light use).

## Why Deploy

Jitsi's docker setup assumes UDP on port 10000, which Railway cannot route — stock deployments lose audio/video as soon as a third participant joins (the moment the bridge, not peer-to-peer, carries media). This template ships the one architecture that works on Railway: TURN-over-TCP with XEP-0215 ephemeral credentials generated from a per-deployment shared secret. You get the full self-host stack — your own domains, your own accounts, no participant limits imposed by a vendor, guest access with moderator control — deployed in one click instead of a five-service, password-plumbed manual setup.

## Common Use Cases

- Private team meetings and standups on your own infrastructure
- Client calls where you control the domain and branding (moderator accounts, guest links)
- Community/community-group conferencing with guest access and lobby control
- A baseline to extend with Jibri recording, Etherpad, or live streaming later

## Dependencies for

### Deployment Dependencies

- **Railway TCP proxy** — created for coturn (application port 3478); Railway assigns the public domain/port automatically and the stack references them via expressions, so fresh deploys always get the correct TURN address.
- **Generated secrets** — `JICOFO_AUTH_PASSWORD`, `JVB_AUTH_PASSWORD`, `TURN_SHARED_SECRET` are created per deployment by Railway `${{secret(...)}}` expressions; prosody, jicofo, jvb, and coturn receive matching values via variable references.
- **One post-deploy step** — create your first moderator account in the prosody shell:
  `prosodyctl --config /run/prosody/config/prosody.cfg.lua shell user create admin@meet.jitsi 'YourStrongPassword'`
- **RAM** — jvb is capped at 2 GB with `VIDEOBRIDGE_MAX_MEMORY=1792m`; do not lower the jvb service below 2 GB.
- **No DNS setup** — the web service gets a Railway domain automatically; bring a custom domain later if you want.
