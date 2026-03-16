# Trident Monitoring

Grafana dashboard and Prometheus configuration for [Trident Cache Proxy](https://github.com/Trident-Cache/trident-cache).

## Quick Start

1. Make sure Trident is running with metrics enabled:

```toml
[metrics]
enabled = true
address = "127.0.0.1:9190"
```

2. Start the monitoring stack:

```bash
docker compose up -d
```

3. Open Grafana at `http://localhost:3000` (default credentials: `admin` / `trident`).

The Trident dashboard is auto-provisioned — no manual import needed.

## What's Included

- **Grafana dashboard** (`grafana-dashboard.json`) — hit rate, request breakdown, memory components, backend health, SWR refresh queue, compression, and more.
- **Prometheus config** (`prometheus.yml`) — scrapes Trident's `/metrics` endpoint every 15s.
- **Docker Compose** — one-command setup with auto-provisioned datasource and dashboard.

## Manual Import

If you already have Grafana running, import `grafana-dashboard.json` via Grafana UI:

1. Go to Dashboards → Import
2. Upload `grafana-dashboard.json`
3. Select your Prometheus datasource

## Configuration

Edit `prometheus.yml` to match your Trident instance:

```yaml
static_configs:
  - targets: ['127.0.0.1:9190']  # Trident metrics address
```

For multiple Trident instances:

```yaml
static_configs:
  - targets:
      - 'trident-01:9190'
      - 'trident-02:9190'
```

## Dashboard Panels

| Section | Panels |
|---------|--------|
| Overview | Hit Rate, Requests/sec, Process Memory, Cache Entries, Uptime |
| Request Breakdown | Cache Hits, Stale (SWR), Cache Misses |
| Memory | Process Memory (RSS/HWM/Virtual), Cache Storage Used |
| Backend & Cache | Backend Requests, Cache Operations (stores/evictions) |
| Refresh Queue (SWR) | Queue Pending, Completed, Failed, Refresh Rate |
| Compression & Backend | Compression Ratio, Bytes Saved, Backend Health, Connections, Errors |
| Memory Breakdown | Per-component memory (bodies, headers, keys, tags, overhead), Tracked vs Untracked, Allocator stats, Evictions |

## Security

Trident's metrics endpoint (`/metrics`) has **no authentication**. It exposes operational data such as cache hit rates, memory usage, backend health, and request counts. Always bind it to `127.0.0.1` — never expose it to the public internet.

```toml
# Correct — localhost only
[metrics]
address = "127.0.0.1:9190"

# WRONG — accessible from anywhere
# address = "0.0.0.0:9190"
```

The same applies to the admin API:

```toml
[admin]
address = "127.0.0.1:9301"
auth_token = "your-secret-token"
```

If you need to access metrics remotely, use an SSH tunnel or place them behind a reverse proxy with authentication (see the [Trident documentation](https://github.com/Trident-Cache/trident-cache) for examples).

## Requirements

- Trident 1.0.5+ (for full metrics support)
- Trident 1.0.7+ (for per-component memory breakdown)
- Docker and Docker Compose
