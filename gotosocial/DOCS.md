# Home Assistant App: GoToSocial

[GoToSocial][gotosocial] is a lightweight ActivityPub server — your own
single-user fediverse instance, compatible with Mastodon clients and able
to federate with Mastodon, Pixelfed, and the rest of the fediverse. It is
a single Go binary backed by SQLite, so the whole instance lives in this
one container and rides along in Home Assistant backups.

## Important: read this before first start

A fediverse server must be reachable from the public internet at a stable
hostname, and that hostname (plus the optional account domain) is
**permanent once you federate** — other servers cache it forever. Decide
on your `host` (e.g. `social.home.example.com`) and whether you want a
[split account domain](#split-account-domain) *before* starting the app
for the first time.

Home Assistant ingress and Nabu Casa remote access cannot carry
federation traffic: federation requires anonymous public access, which is
exactly what those layers are designed to prevent. You need one of the
[networking setups](#getting-traffic-to-the-instance) below.

## Configuration

Example configuration:

```yaml
host: social.home.example.com
port: 8080
letsencrypt: false
trusted_proxies:
  - 172.16.0.0/12
log_level: info
initial_account_username: chandler
initial_account_email: me@example.com
initial_account_password: changeme-very-secret
route53_ddns:
  enabled: true
  zone_id: Z0123456789ABCDEFGHIJ
  record: origin.social.home.example.com
  aws_access_key_id: AKIA...
  aws_secret_access_key: "..."
  interval: 300
  ttl: 60
```

### Option: `host` (required)

The public hostname the instance is served on. Permanent after first
federation.

### Option: `account_domain`

Optional [split-domain][gts-split-domain] setup: serve the API on `host`
but mint usernames like `@you@example.com`. Requires webfinger redirects
on the account domain (see GoToSocial docs) and must be configured before
first start.

### Option: `port`

The port GoToSocial binds on the host network. Default `8080`. Set it to
`443` when `letsencrypt` is enabled so the instance serves HTTPS
directly.

### Option: `letsencrypt` / `letsencrypt_email`

When enabled, GoToSocial obtains and renews its own certificate for
`host` via Let's Encrypt. Requires ports 80 (challenge) and `port`
(normally 443) to be reachable from the internet — i.e. the
[direct port-forward setup](#option-b-direct-port-forward). Leave
disabled when running behind CloudFront or another TLS-terminating proxy.

### Option: `trusted_proxies`

CIDR ranges of reverse proxies in front of the instance, used for correct
client IPs and rate limiting. Needed for the CloudFront setup.

### Option: `landing_page_user`

Username whose profile the instance landing page redirects to — nice for
a single-user instance.

### Options: `initial_account_*`

If all three are set, the app creates that account and promotes it to
admin on first start, so the instance is usable without ever opening a
shell. Registration is closed by default. Remove the password from the
options after first start if you like; the account is only created once.

### Options: `route53_ddns`

Keeps a Route53 AAAA record pointed at this host's public IPv6 address —
the glue for the [CloudFront IPv6 origin setup](#option-a-cloudfront--ipv6-origin).
Create an IAM user whose policy allows only
`route53:ChangeResourceRecordSets` on the one hosted zone. `record`
defaults to `host`; point it at the *origin* record when CloudFront sits
in front.

## Getting traffic to the instance

### Option A: CloudFront + IPv6 origin (no public IPv4 needed, ~$0/mo)

For connections behind CGNAT that have native IPv6. CloudFront supports
IPv6-only origins, so it can reach your home over IPv6 while the rest of
the fediverse reaches CloudFront over either protocol.

1. Enable `route53_ddns` with `record: origin.<host>` — the app keeps
   that AAAA record pointed at your home IPv6 address.
2. Open TCP `port` through your router's IPv6 firewall to the Home
   Assistant box (no NAT involved).
3. Create a CloudFront distribution: alternate domain `host` with a free
   ACM certificate, origin `origin.<host>` with IPv6-only connectivity,
   caching disabled, all HTTP methods and WebSockets allowed, all viewer
   headers/cookies/query strings forwarded to the origin.
4. Alias `host` to the distribution in Route53.
5. Leave `letsencrypt` disabled and add CloudFront's address ranges (or
   your choice of CIDRs) to `trusted_proxies`.

### Option B: direct port forward (public IPv4 required, $0/mo)

1. A/AAAA record for `host` pointing at your home address (dynamic DNS
   as needed — `route53_ddns` can keep the AAAA current).
2. Forward TCP 443 and 80 on your router to the Home Assistant box.
3. Set `port: 443`, `letsencrypt: true`, and `letsencrypt_email`.

### Option C: tunnel or VPS relay

A Cloudflare Tunnel, or a small VPS relaying a WireGuard/rathole tunnel,
also works: terminate the public side there and forward to `port` on
this host. Configure `trusted_proxies` accordingly.

## First start

1. Configure `host` (and `account_domain` if splitting), networking, and
   the `initial_account_*` options.
2. Start the app and watch the log.
3. Log in at `https://<host>/` with a Mastodon client or the web
   settings panel at `https://<host>/settings`.

## Backups

Everything — SQLite database, media, certificates — lives in `/data`,
so a Home Assistant backup of this app is a complete instance backup.
Your fediverse identity is unrecoverable without it; keep backups on.

## Known limitations

- No Home Assistant ingress: GoToSocial (like all ActivityPub servers)
  must be served from the root of its hostname, which ingress's
  token-prefixed paths cannot do. Use the public hostname instead.
- The app uses host networking, so `port` (and 80/443 with Let's
  Encrypt) must be free on the host.
- Single-user by intent: registration is closed by default; additional
  accounts can be created the same way via the GoToSocial CLI.

[gotosocial]: https://docs.gotosocial.org/
[gts-split-domain]: https://docs.gotosocial.org/en/latest/advanced/host-account-domain/
