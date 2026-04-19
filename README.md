# Grafana Observability Stack

Self-hosted observability stack using Grafana, Loki, Prometheus, and Alloy — all running via Docker Compose with nginx basic auth for public exposure.

## Architecture

```
[Debian Server — bare metal]
  └── Grafana Alloy (installed as systemd service)
        ├── Tails logs → Loki (via nginx basic auth)
        └── Scrapes system metrics → Prometheus (via nginx basic auth)

[Docker Host]
  ├── Grafana    → :3000
  ├── Loki       → :3100 (internal), :3101 (nginx proxy)
  └── Prometheus → :9090 (internal), :9091 (nginx proxy)
```

## Components

| Component | Description | Directory |
|-----------|-------------|-----------|
| Grafana | Dashboards and visualization | [`grafana/`](grafana/) |
| Loki | Log aggregation | [`loki/`](loki/) |
| Prometheus | Metrics storage (OTLP + remote write) | [`prometheus/`](prometheus/) |
| Alloy | Agent: log shipping + metrics scraping (bare metal, not containerized) | [`alloy/`](alloy/) |

## Quick Start

### 1. Start the stack

```bash
cd grafana && docker compose up -d
cd ../loki && docker compose up -d
cd ../prometheus && docker compose up -d
```

### 2. Configure nginx

Each service has an nginx config for public exposure with basic auth:

```bash
sudo cp loki/loki-nginx.conf /etc/nginx/conf.d/
sudo cp prometheus/prometheus-nginx.conf /etc/nginx/conf.d/
sudo htpasswd -c /etc/apache2/.htloki loki
sudo htpasswd -c /etc/apache2/.htprometheus prometheus
sudo nginx -s reload
```

### 3. Deploy Alloy

On the target Debian server, configure and run Grafana Alloy using [`alloy/config.yaml`](alloy/config.yaml) as a base. See [`alloy/README.md`](alloy/README.md) for details.

## Access

| Service | URL |
|---------|-----|
| Grafana | http://localhost:3000 (default: `admin` / `admin`) |
| Loki | https://loki.sabihinmolang.eu.org (basic auth) |
| Prometheus | https://prometheus.sabihinmolang.eu.org (basic auth) |

## Secrets

Credentials are stored in `.env` and `*.secret` files, both excluded from version control via `.gitignore`.
