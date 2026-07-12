# Home Assistant App: BirdWeather Exporter

This app runs the [BirdWeather Prometheus exporter][exporter]: it polls
the [BirdWeather API][birdweather-api] for your station (e.g. a
BirdWeather PUC) on a configurable interval, caches the result, and
serves Prometheus metrics on port `9743`.

## Configuration

Example configuration:

```yaml
station_token: abcdefgh12345678
query_interval: 5m
period: day
species_limit: 100
```

### Option: `station_token`

**Required.** Your BirdWeather station token, found on your station's
page at [app.birdweather.com][birdweather-app] or in the BirdWeather
mobile app. Treat it like a password — anyone with the token can read
your station's data.

### Option: `query_interval`

How often the BirdWeather API is queried, as a duration like `30s`,
`5m` or `1h`. Default `5m`. Prometheus can scrape the app as often as it
likes regardless — results are cached between API queries.

### Option: `period`

The rolling window the detection counts cover: `day`, `week`, `month`
or `all`. Default `day`. Counts are exposed as gauges and shrink as old
detections age out of the window.

### Option: `species_limit`

Maximum number of species fetched per query, between 1 and 100 (the API
maximum). Default `100`.

## Metrics

The main metric is `birdweather_detections_count` with a `species`
label (plus `scientific_name`, `species_id` and `period`). Also
exported: per-species confidence-bucket counts, last-detection
timestamps, station-wide totals, and exporter health metrics such as
`birdweather_query_success`. See the [exporter documentation][exporter]
for the full list.

## Scraping it from the Prometheus app

Add a scrape job to the Prometheus app's options pointing at this app's
internal hostname — the add-on slug with `_` replaced by `-`, visible in
the URL of this app's page in Home Assistant:

```yaml
scrape_configs:
  - job_name: birdweather
    targets:
      - <repo-hash>-birdweather-exporter:9743
    scrape_interval: 60
```

This works entirely on the internal add-on network without exposing any
port on the host. To scrape from a Prometheus running elsewhere on your
network instead, map port `9743` in this app's network settings — it is
disabled by default. The metrics are not sensitive beyond revealing
which birds visit your yard, but the endpoint has no authentication of
its own.

## Example queries

```promql
# Top 10 species today
topk(10, birdweather_detections_count)

# Minutes since a Northern Cardinal was last heard
(time() - birdweather_species_latest_detection_timestamp_seconds{species="Northern Cardinal"}) / 60

# Alert if the exporter can't reach the BirdWeather API
birdweather_query_success == 0
```

## Known limitations

- The exporter reports what the BirdWeather cloud has processed for your
  station; it does not talk to the PUC directly, so detections appear
  with the usual upload-and-process delay.
- Detection counts are per rolling window, not lifetime counters — use
  gauges semantics (`max_over_time`, not `rate`) in queries.

[birdweather-api]: https://app.birdweather.com/api/index.html
[birdweather-app]: https://app.birdweather.com/
[exporter]: https://github.com/chaukap/birdweather-prometheus-exporter
