# Setup & Installation

## Prerequisites

| Requirement | Notes |
|-------------|-------|
| Python 3.10+ | `pyproject.toml` → `requires-python = ">=3.10"` |
| Network access to Jamf Pro | HTTPS to your instance URL |
| Jamf API credentials | OAuth client credentials (preferred) or API user Basic auth |
| Read privileges | API role must have Read access for each object type you export |
| macOS (optional) | Required for Swift `JamfBackup/` app and Swift parity tests |

## Clone Repository

```bash
git clone https://github.com/roto31/Jamf-Settings-Analysis.git
cd Jamf-Settings-Analysis
```

## Virtual Environment

```bash
python3 -m venv .venv
source .venv/bin/activate   # macOS/Linux
pip install -e .
```

Installs `jamf_exporter` with dependencies: `requests>=2.32.0`, `cryptography>=42.0.0`.

For development:

```bash
pip install -e ".[dev]"
```

## Configuration

### 1. Copy the example env file

```bash
cp config/export.example.env config/export.env
```

`config/export.env` is gitignored and must never be committed.

### 2. Edit `config/export.env`

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `JAMF_URL` | Yes | — | Jamf Pro base URL |
| `JAMF_CLIENT_ID` | OAuth path | — | OAuth API client ID |
| `JAMF_CLIENT_SECRET` | OAuth path | — | OAuth API client secret |
| `JAMF_USERNAME` | Fallback | — | API user for Basic-to-token auth |
| `JAMF_PASSWORD` | Fallback | — | API user password |
| `JAMF_VERIFY_TLS` | No | `true` | Set `false` for self-signed on-prem certificates |
| `JAMF_TIMEOUT_SECONDS` | No | `60` | HTTP request timeout |
| `JAMF_MAX_RETRIES` | No | `3` | Max retry count for 5xx responses |
| `JAMF_RETRY_BACKOFF_SECONDS` | No | `2` | Backoff multiplier between retries |
| `JAMF_ALLOW_RESTORE` | No | `false` | Must be `true` for non-dry-run restore |

Source: `config/export.example.env`, `jamf_exporter/config.py`.

### 3. Optional — Full backup / DR tier

| Variable | Default | Description |
|----------|---------|-------------|
| `JAMF_SSH_HOST` | — | Jamf Pro server hostname (on-prem package fetch) |
| `JAMF_SSH_USER` | — | SSH username |
| `JAMF_SSH_PORT` | `22` | SSH port |
| `JAMF_SSH_IDENTITY_FILE` | — | Path to SSH private key |
| `JAMF_DR_PASSPHRASE` | — | Passphrase for secrets vault encryption |
| `JAMF_RESTORE_TARGET_URL` | — | Target URL for live restore (safety gate) |
| `JAMF_FIXTURE_ROOT` | — | Offline fixture directory for testing |

### 4. Load environment before running

```bash
set -a && source config/export.env && set +a
```

## On-Prem / Self-Signed TLS

```bash
JAMF_VERIFY_TLS="false"
```

When TLS verification is disabled, `orchestrator.py` suppresses `urllib3` `InsecureRequestWarning`.

## Jamf API Client Setup (Jamf Admin)

1. In Jamf Pro: **Settings → System Settings → API Roles and Clients**
2. Create an API client (OAuth) or ensure your API user has a role with **Read** privileges
3. Record credentials in `config/export.env`

Missing Read privileges produce HTTP 401 on specific endpoints, logged in `output/logs/export.log`.

## Verify Installation

```bash
pytest -q
```

Tests cover auth, config, manifest, redaction, registry, documentation builder, output indexes, inventory, package fetcher, restore ordering, export parity fixtures, and failures/compatibility.

## Sync Endpoint Registry (Optional)

After modifying endpoint specs:

```bash
python scripts/sync_endpoint_registry.py
```

## Optional: jamf_mcp Subproject

See [jamf_mcp/docs/INSTALLATION.md](../jamf_mcp/docs/INSTALLATION.md).

## Optional: JamfBackup Swift App

```bash
cd JamfBackup && swift build && swift run JamfBackup
```

See [JamfBackup README](../JamfBackup/README.md).
