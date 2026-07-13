# Chandler Haukap's Home Assistant Apps

A collection of Home Assistant add-ons (apps), distributed through this
single repository — the analogue of [hassio-addons/repository][upstream].

## Installation

1. Add this repository to your Home Assistant add-on store:

   [![Add repository to my Home Assistant][repo-badge]][repo-url]

   Or manually: **Settings → Add-ons → Add-on Store → ⋮ → Repositories**
   and add:

   ```txt
   https://github.com/chaukap/homeassistant-apps
   ```

2. The add-ons below will appear in the store under
   "Chandler Haukap's Home Assistant Apps".

Add-ons in this repository have no `image` key in their configuration, so
Home Assistant builds them locally on your machine at install time. No
registry credentials are required; the first install of an add-on takes a
few minutes while its container image is built.

## Add-ons

### [Grafana](grafana/)

![Latest Version][grafana-version-shield]
![Supports aarch64][aarch64-shield] ![Supports amd64][amd64-shield]

The open platform for beautiful analytics and monitoring, with persistent
plugins and datasources, file-based provisioning, and a built-in image
renderer. Developed in [chaukap/addon-grafana][addon-grafana].

### [Prometheus](prometheus/)

![Latest Version][prometheus-version-shield]
![Supports aarch64][aarch64-shield] ![Supports amd64][amd64-shield]

The systems monitoring toolkit and time-series database. Scrapes
metrics from Home Assistant itself (via the Supervisor API, no access
token needed) and any other targets on your network, with the web UI
available through Ingress — the natural data source for the Grafana
add-on. Developed directly in this repository.

### [BirdWeather Exporter](birdweather_exporter/)

![Latest Version][birdweather-version-shield]
![Supports aarch64][aarch64-shield] ![Supports amd64][amd64-shield]

Prometheus exporter for BirdWeather stations like the PUC — polls the
BirdWeather API and exposes per-species detection counts, confidence
breakdowns, and station totals as metrics, ready to be scraped by the
Prometheus add-on and graphed in Grafana. Developed in
[chaukap/birdweather-prometheus-exporter][birdweather-exporter].

### [GoToSocial](gotosocial/)

![Latest Version][gotosocial-version-shield]
![Supports aarch64][aarch64-shield] ![Supports amd64][amd64-shield]

Your own single-user fediverse instance — a lightweight, Mastodon-API
compatible ActivityPub server, self-contained in one container (SQLite,
no external database) with an optional Route53 dynamic-DNS sidecar for
CloudFront IPv6-origin deployments. Developed directly in this
repository.

## Development workflow

Each add-on is developed in its own repository; this repository is the
distribution point that Home Assistant installs from.

To release an add-on update, sync its folder from the development
repository into this one, e.g. for Grafana:

```bash
rsync -a --delete ../addon-grafana/grafana/ grafana/
git commit -am "⬆️ Update Grafana add-on"
```

Bump the `version` in the add-on's `config.yaml` as part of the change in
the development repository — Home Assistant only offers updates to
installed add-ons when the version changes.

## License

MIT License — see [LICENSE](LICENSE).

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[addon-grafana]: https://github.com/chaukap/addon-grafana
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[gotosocial-version-shield]: https://img.shields.io/badge/version-0.21.2.1-blue.svg
[birdweather-exporter]: https://github.com/chaukap/birdweather-prometheus-exporter
[birdweather-version-shield]: https://img.shields.io/badge/version-0.1.0-blue.svg
[grafana-version-shield]: https://img.shields.io/badge/version-13.0.1-blue.svg
[prometheus-version-shield]: https://img.shields.io/badge/version-3.5.0-blue.svg
[repo-badge]: https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg
[repo-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fchaukap%2Fhomeassistant-apps
[upstream]: https://github.com/hassio-addons/repository
