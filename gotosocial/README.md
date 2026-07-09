# Home Assistant App: GoToSocial

Your own single-user fediverse instance — a lightweight, Mastodon-API
compatible ActivityPub server in one self-contained container.

## About

[GoToSocial](https://docs.gotosocial.org/) is a fast, small ActivityPub
server written in Go. It federates with Mastodon and the rest of the
fediverse, works with Mastodon phone apps, and needs only SQLite — so the
entire instance (database, media, certificates) lives in this app's data
directory and is captured by ordinary Home Assistant backups.

The app bundles an optional Route53 dynamic-DNS sidecar that keeps an
AAAA record pointed at your home IPv6 address, enabling a
CloudFront-fronted deployment with no dedicated IPv4 address and no
monthly cost.

See [DOCS.md](DOCS.md) for configuration and the supported networking
setups.
