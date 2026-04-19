# Prometheus

Prometheus instance configured to receive OTLP push data, exposed via nginx with basic auth.

## Setup

### 1. Start Prometheus

```bash
docker compose up -d
```

Prometheus runs on port `9090` with:
- Remote write receiver enabled (`--web.enable-remote-write-receiver`)
- Config mounted from `./prometheus.yml`

### 2. Configure nginx

Copy the nginx config and enable basic auth:

```bash
sudo cp prometheus-nginx.conf /etc/nginx/conf.d/
sudo htpasswd -c /etc/apache2/.htprometheus prometheus
sudo nginx -s reload
```

Prometheus is then proxied on port `9091` at `prometheus.sabihinmolang.eu.org` with basic auth.

## Configuration

### prometheus.yml

Based on the [official OTLP example](https://github.com/prometheus/prometheus/blob/main/documentation/examples/prometheus-otlp.yml). Key settings:

- **OTLP ingestion** — promotes common resource attributes (service, k8s, cloud, container) to labels
- **Translation strategy** — `NoUTF8EscapingWithSuffixes` to preserve metric/label names
- **Out-of-order ingestion** — 30m window to handle OTLP push timing

### Ports

| Port | Description |
|------|-------------|
| `9090` | Prometheus (internal, Docker) |
| `9091` | Prometheus (public, nginx proxy with basic auth) |

## Sending OTLP Metrics

Point your OTLP exporter at:

```
https://prometheus.sabihinmolang.eu.org:9091/api/v1/write
```

Include basic auth credentials in the request.
