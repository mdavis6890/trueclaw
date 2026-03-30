# trueclaw

Docker Compose deployment of [OpenClaw](https://github.com/openclaw/openclaw) on TrueNAS Scale, run as a Custom Application.

## Prerequisites

- TrueNAS Scale with Custom Applications enabled
- Two ZFS datasets created on your pool (config and workspace)
- A secrets env file on the host (see below)

## Setup

### 1. Create the secrets file

Create `/mnt/<pool>/enc1/openclaw.env` (or your preferred path), owned by root and chmod 600:

```env
OPENCLAW_TOKEN=your-token-here
# add any other required secrets
```

### 2. Create host directories

Run once on the TrueNAS host to set correct ownership (UID/GID 3004):

```bash
chown -R 3004:3004 /mnt/<pool>/openclaw-config /mnt/<pool>/openclaw-workspace
chmod 700 /mnt/<pool>/openclaw-config
```

### 3. Deploy

In TrueNAS, go to **Apps → Custom App** and paste in `docker-compose.yml`, or copy the file to your TrueNAS host and run:

```bash
docker compose up -d
```

OpenClaw will be available at `http://<truenas-ip>:18789`.

## Configuration

| Variable | Default | Description |
|---|---|---|
| `OPENCLAW_CONFIG_PATH` | `/mnt/files01/enc1/openclaw-config` | Host path for config dataset |
| `OPENCLAW_WORKSPACE_PATH` | `/mnt/files01/enc1/openclaw-workspace` | Host path for workspace dataset |

Override these by setting them in the env file or in your shell before running `docker compose`.

## Security notes

- Token auth is enforced via `OPENCLAW_AUTH_MODE: token` — do not remove this.
- Exec, filesystem write, and browser tools are disabled by default (`OPENCLAW_TOOL_POLICY`). Re-enable selectively in `openclaw.json` after reviewing the risks.
- The container runs as a non-root user (UID/GID 3004) with all capabilities dropped.
- The Docker socket is **not** mounted. Do not add it unless absolutely necessary — it grants root-equivalent access to the TrueNAS host.

## Useful commands

```bash
# View logs
docker compose logs -f openclaw

# Stop
docker compose down

# Restart
docker compose restart openclaw
```
