# Setup & Installation

## Prerequisites

| Requirement | Notes |
|-------------|-------|
| Python 3.10+ | Verified with `pyproject.toml` `requires-python = ">=3.10"` |
| Network access to Jamf Pro | HTTPS to your instance URL |
| Jamf API credentials | OAuth client credentials (preferred) or API user Basic auth |
| Read privileges | API role must have Read access for each object type you export |

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

This installs the `jamf_exporter` package and its dependency `requests>=2.32.0`.

## Configuration

### 1. Copy the example env file

```bash
cp config/export.example.env config/export.env
```

`config/export.env` is gitignored and must never be committed.

### 2. Edit `config/export.env`

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `JAMF_URL` | Yes | — | Jamf Pro base URL (e.g. `https://your.jamfcloud.com` or on-prem with port) |
| `JAMF_CLIENT_ID` | OAuth path | — | OAuth API client ID |
| `JAMF_CLIENT_SECRET` | OAuth path | — | OAuth API client secret |
| `JAMF_USERNAME` | Fallback | — | API user for Basic-to-token auth |
| `JAMF_PASSWORD` | Fallback | — | API user password |
| `JAMF_VERIFY_TLS` | No | `true` | Set `false` for self-signed on-prem certificates |
| `JAMF_TIMEOUT_SECONDS` | No | `60` | HTTP request timeout |
| `JAMF_MAX_RETRIES` | No | `3` | Max retry count for 5xx responses |
| `JAMF_RETRY_BACKOFF_SECONDS` | No | `2` | Backoff multiplier between retries |
| `JAMF_ALLOW_RESTORE` | No | `false` | Must be `true` for non-dry-run restore (scaffolding only) |

Source: `config/export.example.env`, `jamf_exporter/config.py`.

### 3. Load environment before running

```bash
set -a && source config/export.env && set +a
```

Alternatively, export variables in your shell profile or CI secret store.

## On-Prem / Self-Signed TLS

For on-prem Jamf with a self-signed certificate:

```bash
JAMF_VERIFY_TLS="false"
```

When TLS verification is disabled, `orchestrator.py` suppresses `urllib3` `InsecureRequestWarning` spam.

## Jamf API Client Setup (Jamf Admin)

1. In Jamf Pro: **Settings → System Settings → API Roles and Clients**
2. Create an API client (OAuth) or ensure your API user has a role with **Read** privileges for all object types you need
3. Record `client_id` and `client_secret` in `config/export.env`

Missing Read privileges produce HTTP 401 on specific endpoints. The exporter logs these in `output/logs/export.log` and prints a summary at the end of the run.

## Verify Installation

```bash
pytest -q
```

Expected: 8 tests pass (auth, config, manifest, redaction, registry).

## Optional: jamf_mcp Subproject

The `jamf_mcp/` directory is a separate MCP server package. See [jamf_mcp/docs/INSTALLATION.md](../jamf_mcp/docs/INSTALLATION.md) for its own setup.
