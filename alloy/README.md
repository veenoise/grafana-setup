# Grafana Alloy

Grafana Alloy agent installed bare metal (not containerized) on a Debian server as a systemd service. It collects logs and system metrics, forwarding them to Loki and Prometheus respectively.

## What it does

- Tails log files and ships them to Loki
- Scrapes system metrics via the Unix exporter and remote-writes them to Prometheus

## Configuration

Use `config.yaml` as your base. Copy it and fill in your values.

### 1. Log files

Update the path glob to match the logs you want to ship:

```hcl
local.file_match "local_files" {
  path_targets = [{"__path__" = "/var/log/nginx/wordpress.*.log"}]
  sync_period = "5s"
}
```

### 2. Loki endpoint

Set your Loki push URL and credentials:

```hcl
loki.write "grafana_loki" {
  endpoint {
    url = "https://<loki-host>/loki/api/v1/push"

    basic_auth {
      username = "<username>"
      password = "<password>"
    }
  }
}
```

### 3. Prometheus remote write

Set your Prometheus remote write URL and credentials:

```hcl
prometheus.remote_write "metrics_service" {
  endpoint {
    url = "https://<prometheus-host>/api/v1/write"

    basic_auth {
      username = "<username>"
      password = "<password>"
    }
  }
}
```

## Files

| File | Description |
|------|-------------|
| `config.yaml` | Main config with placeholder credentials — safe to commit |
