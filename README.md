# rybbit-digitalocean

Desired state for a production-oriented single-node Rybbit web & product analytics deployment on DigitalOcean.

## Architecture

- **Domain**: `https://rybbit.bigconfig.online`
- **Location**: `ams3` (Amsterdam)
- **Droplet**: `s-4vcpu-8gb` on Ubuntu 24.04
- **Databases**:
  - PostgreSQL 17 (`postgres:17-alpine`)
  - ClickHouse 24.8 (`clickhouse/clickhouse-server:24.8-alpine`)
  - Redis (`redis:8.6.4-alpine`)
- **Ingress**: Caddy origin TLS terminating on 80/443
- **Backups**: Daily systemd timer uploading encrypted backups to Cloudflare R2

## Usage

```sh
./green build
./green create --dry-run
./green create
```

## Operations & Verification

```sh
# Health check
curl -fsS https://rybbit.bigconfig.online/api/health

# Send synthetic test event
curl -fsS -X POST -H 'content-type: application/json' \
  --data '{"name":"pageview","site_id":"benchmark","data":{"path":"/test"}}' \
  https://rybbit.bigconfig.online/api/track

# Run backup service on host
ssh root@SERVER 'systemctl start rybbit-backup.service'
ssh root@SERVER 'systemctl status rybbit-backup.timer'
```

## License

MIT License. Copyright (c) 2026 getcolors.
