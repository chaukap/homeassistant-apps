# Home Assistant App: Prometheus

The systems monitoring toolkit — scrape metrics from Home Assistant and
anything else on your network into one time-series database.

## About

[Prometheus](https://prometheus.io/) collects metrics by scraping HTTP
endpoints and stores them in its own time-series database, with the
PromQL query language and a built-in web UI on top. It is the natural
companion to the Grafana app in this repository: Prometheus gathers and
stores the numbers, Grafana draws them.

Out of the box the app scrapes itself and — through the Supervisor API,
with no access token to manage — Home Assistant's own `prometheus:`
integration. Additional targets are added either as simple options or as
full Prometheus scrape configurations dropped into the app's config
directory. The database lives in `/data`, so it is captured by ordinary
Home Assistant backups.

See [DOCS.md](DOCS.md) for configuration details.
