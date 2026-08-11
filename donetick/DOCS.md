# Home Assistant App: DoneTick

[DoneTick][donetick] is an open-source task and chore management app —
recurring tasks with flexible schedules, shared "circles" for the
household, assignee rotation, points, and due-date notifications. It is
a single Go binary with the web frontend embedded, backed by SQLite in
the app's `/data` volume, so everything is captured by ordinary Home
Assistant backups.

## First start

1. Start the app and open the **Web UI** (port `2021` on your Home
   Assistant host by default).
2. Create your account with **Sign up** — accounts are local to this
   instance.
3. Once everyone in your household has an account, consider setting
   `disable_signups: true` so strangers on your network can't register.

The web UI is served on the host network port, not through ingress:
DoneTick's frontend expects to live at the root of its own origin and
cannot be served under Home Assistant's ingress path prefix.

Authentication is DoneTick's built-in username/password login only —
this app deliberately does not wire up OIDC/OAuth.

## Configuration

Example configuration:

```yaml
public_host: http://homeassistant.local:2021
disable_signups: false
telegram_token: "123456:ABC-DEF..."
log_level: info
```

### Option: `public_host`

The base URL your users reach DoneTick on, used when the app needs to
build absolute links (for example in notifications). Optional; plain
in-browser use works without it.

### Option: `disable_signups`

When `true`, the registration form is disabled and no new accounts can
be created. Existing accounts keep working. Leave it `false` until
everyone has signed up, then turn it on.

### Option: `jwt_secret`

Secret used to sign login session tokens. Leave it unset and the app
generates a random one on first start and keeps it in `/data`, so
sessions survive restarts and updates. Set it only if you want to
control the value yourself (must be at least 32 characters).

### Option: `telegram_token`

A Telegram bot token. With it set, users can link their Telegram
account in DoneTick's settings and receive due/overdue notifications
from your bot.

### Option: `pushover_token`

A Pushover application token, enabling Pushover notifications the same
way.

### Option: `log_level`

Log verbosity of the DoneTick server: `debug`, `info`, `warn`, or
`error`. Defaults to `info`.

## Network

The web UI and API are served on container port `2021`, published on
host port `2021` by default — change the host side in the app's
Network section if it collides with something else. Other devices on
your LAN (and the DoneTick mobile app) can reach it at
`http://<home-assistant-ip>:2021`.

## Data and backups

Everything lives in `/data`:

- `donetick.db` — the SQLite database (tasks, circles, users, points)
- `.jwt_secret` — the generated session-signing secret

Both are included in Home Assistant backups automatically. To start
over completely, uninstall the app (which removes `/data`) and
reinstall.

## Updating

The app version tracks the upstream DoneTick release. Database schema
migrations run automatically on start after an update.

[donetick]: https://github.com/donetick/donetick
