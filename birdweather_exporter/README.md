# Home Assistant App: BirdWeather Exporter

Prometheus exporter for BirdWeather stations — get your backyard birds
into Prometheus and Grafana.

## About

[BirdWeather](https://www.birdweather.com/) stations like the PUC listen
for birds around the clock and identify them with the BirdNET neural
network. This app polls the BirdWeather API for your station and exposes
the detections as Prometheus metrics: detection counts per species,
confidence breakdowns, last-heard timestamps, and station totals.

It pairs naturally with the Prometheus and Grafana apps in this
repository: point Prometheus at this exporter and every bird in your
yard becomes a time series. The exporter itself is developed in
[chaukap/birdweather-prometheus-exporter][exporter], and this app runs
the released container image.

See [DOCS.md](DOCS.md) for configuration details.

[exporter]: https://github.com/chaukap/birdweather-prometheus-exporter
