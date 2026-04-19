# Grafana Docker Setup

Runs Grafana Enterprise using Docker Compose.

## Requirements

- Docker
- Docker Compose

## Usage

**Start:**
```bash
docker compose up -d
```

**Stop:**
```bash
docker compose down
```

Access Grafana at [http://localhost:3000](http://localhost:3000)

Default credentials: `admin` / `admin`

## Configuration

| Setting | Value |
|---|---|
| Image | `grafana/grafana-enterprise` |
| Container name | `grafana` |
| Port | `3000` |
| Restart policy | `unless-stopped` |
| Data volume | `grafana-storage` |
| Root URL | `https://grafana.sabihinmolang.eu.org` |
| Domain | `grafana.sabihinmolang.eu.org` |
| Enforce domain | `true` |

Host machine is accessible from within the container via `host.docker.internal`.
