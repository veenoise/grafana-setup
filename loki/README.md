# Loki

Grafana Loki instance for log aggregation, exposed via nginx with basic auth.

## Setup

### 1. Start Loki

```bash
docker compose up -d
```

Loki runs on port `3100` with:
- Config mounted from `./loki-config.yaml`
- Filesystem storage under `/tmp/loki`

### 2. Configure nginx

Copy the nginx config and enable basic auth:

```bash
sudo cp loki-nginx.conf /etc/nginx/conf.d/
sudo htpasswd -c /etc/apache2/.htloki loki
sudo nginx -s reload
```

Loki is then proxied on port `3101` at `loki.sabihinmolang.eu.org` with basic auth.

## Configuration

### loki-config.yaml

Key settings:

- **Storage** — TSDB index with filesystem chunks under `/tmp/loki`
- **Pattern ingester** — enabled with metric aggregation pointing to `localhost:3100`
- **Results cache** — embedded cache with 100MB limit for query range results
- **Schema** — v13 from 2020-10-24 with 24h index period

### Ports

| Port | Description |
|------|-------------|
| `3100` | Loki (internal, Docker) |
| `3101` | Loki (public, nginx proxy with basic auth) |

## Sending Logs

Point your log shipper (e.g. Promtail, Alloy, or any Loki-compatible client) at:

```
https://loki.sabihinmolang.eu.org:3101/loki/api/v1/push
```

Include basic auth credentials in the request.
