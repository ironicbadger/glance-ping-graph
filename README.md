# ping-graph

A lightweight network latency monitoring tool with SVG graphs. Designed to work as a [Glance](https://github.com/glanceapp/glance) widget or standalone dashboard.

## Features

- Multiple probe types: ICMP ping, TCP connect, DNS lookup
- Smooth SVG graphs with configurable history
- Glance-compatible widget endpoint (`/`)
- Full detail page (`/detail`)
- Multi-arch Docker image (amd64/arm64)

## Usage

```yaml
services:
  ping-graph:
    image: ghcr.io/OWNER/ping-graph:latest
    container_name: ping-graph
    ports:
      - 8080:8080
    environment:
      - TARGETS=1.1.1.1:icmp,google.com:tcp:443,8.8.8.8:dns
      - INTERVAL=30s
      - HISTORY=6h
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `TARGETS` | `8.8.8.8:icmp,1.1.1.1:icmp` | Comma-separated list of targets to probe |
| `INTERVAL` | `60s` | Time between probes (supports `s`, `m`, `h`, `d`) |
| `HISTORY` | `1h` | How long to keep history (supports `s`, `m`, `h`, `d`) |
| `BASE_URL` | `` | Base URL for widget links (e.g., `https://ping.example.com`) |

## Target Format

```
[DisplayName=]host:probe_type[:port]
```

**Probe types:**
- `icmp` - ICMP ping (default)
- `tcp` - TCP connection (default port 443)
- `dns` - DNS resolution

**Examples:**
```
# Simple ICMP ping
1.1.1.1:icmp

# TCP connect to port 443
google.com:tcp:443

# DNS resolution timing
8.8.8.8:dns

# With custom display names
Cloudflare=1.1.1.1:icmp,Google=google.com:tcp:443
```

## Endpoints

| Path | Description |
|------|-------------|
| `/` | Widget view (for Glance embedding) |
| `/detail` | Full-page dashboard with larger graphs |

## Glance Integration

Add to your `glance.yml`:

```yaml
- type: extension
  url: http://ping-graph:8080
  allow-potentially-dangerous-html: true
```

## Development

```bash
# Run locally
pip install -r requirements.txt
TARGETS=1.1.1.1:icmp python main.py

# Build Docker image
docker build -t ping-graph .
docker run -p 8080:8080 -e TARGETS=1.1.1.1:icmp ping-graph
```

## License

MIT
