# Home Assistant App: DoneTick

Task and chore management for you and your household — recurring
schedules, shared circles, and a points system to keep everyone honest.

## About

[DoneTick](https://donetick.com/) is an open-source app for organizing
tasks and chores: flexible recurrence (down to "the second Tuesday of
the month"), circles for sharing chores with family or housemates,
automatic assignee rotation, points, and optional Telegram or Pushover
nudges when things come due. It is a single Go binary with the web
frontend embedded and SQLite underneath, so the whole thing lives in
this one container and rides along in Home Assistant backups.

This app runs the official upstream release binary and stores all data
in the add-on's `/data` volume. Sign-ups use DoneTick's built-in local
accounts; no external identity provider is required or configured.

See [DOCS.md](DOCS.md) for configuration details.
