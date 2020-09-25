# Integrate wmbusmeters with Prometheus
<https://prometheus.io/>

1. Add a shell configuration key to `/etc/wmbusmeters.conf`:
```
shell=/usr/bin/curl localhost:9144/webhook --data "$METER_JSON"
```

2. Create a [grok_exporter](https://github.com/fstab/grok_exporter) configuration:
```
global:
  config_version: 3
input:
  type: webhook
  webhook_path: /webhook
  webhook_format: json_single
  webhook_json_selector: .meter
metrics:
- type: gauge
  name: water_consumption
  help: Cubic meters of water used
  match: 'multical21'
  value: '{{ index .extra "total_m3" }}'
  cumulative: false
  labels:
    media: '{{ index .extra "media" }}'
    id: '{{ index .extra "id" }}'
    name: '{{ index .extra "name" }}'
server:
  host: "[::]"
  port: 9144
```

3. Start `grok_exporter`
```
docker-compose up -d
```
With:
```
version: '3'
services:
  grok_exporter:
    image: magentaaps/grok_exporter:latest-rc
    command: -config /srv/grok_exporter_config.yml
    ports:
      - 9144:9144
    volumes:
      - ./grok_exporter_config.yml:/srv/grok_exporter_config.yml
```

4. Start `wmbusmetersd`

5. Verify that metrics are flowing:
```
curl localhost:9144/metrics
```
Yields:
```
...
# HELP water_consumption Cubic meters of water used
# TYPE water_consumption gauge
water_consumption{id="11111111",media="cold water",name="MyColdTapWater"} 1.326
```

6. Add `grok_exporter` as a `scrape_target` to `Prometheus`.
