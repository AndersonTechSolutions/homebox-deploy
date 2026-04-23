# homebox-deploy

Komodo deploy config for [HomeBox](https://github.com/sysadminsmedia/homebox),
a self-hosted inventory / organization system.

## What lives here

`deploy/compose.homebox.yml` — a production-oriented compose file that pulls
the upstream `ghcr.io/sysadminsmedia/homebox:latest` image. Differs from the
upstream repo's dev-oriented `docker-compose.yml` in:

- Pulls the published image instead of building from source
- `HBOX_MODE=production` (no debug logging)
- Binds SQLite + uploads to `/opt/homebox/data` on the host
- Port exposed only on the LAN IP (`10.10.0.162:3100`), not all interfaces

## How updates flow

| Change | Source | What happens |
|---|---|---|
| Upstream releases a new HomeBox version | GHCR `:latest` tag | Komodo's `poll_for_updates` detects the new digest and redeploys |
| You tweak the compose (ports, env, volume paths) | Push to this repo | Komodo webhook triggers a redeploy |

## Host paths

- `/opt/homebox/data` — SQLite DB + attachments. **Back this up.** Losing it
  is losing every inventory record.
