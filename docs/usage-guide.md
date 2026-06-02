# Usage Guide

## Quick Start

```bash
cd Jamf-Settings-Analysis
source .venv/bin/activate
set -a && source config/export.env && set +a
python scripts/run_full_export.py --output output
```

## CLI Reference

**Script:** `scripts/run_full_export.py`

| Flag | Default | Description |
|------|---------|-------------|
| `--output` | `output` | Root directory for all generated artifacts |
| `--stop-on-401` | off | Stop export immediately on first HTTP 401 (useful for RBAC tuning) |

Source: `scripts/run_full_export.py` lines 11–14.

## End-to-End Walkthrough

### Step 1 — Configure credentials

Edit `config/export.env` with your Jamf URL and OAuth or Basic credentials. See [Setup & Installation](setup-installation.md).

### Step 2 — Run full export

```bash
python scripts/run_full_export.py --output output
```

The orchestrator:

1. Loads config from environment
2. Runs auth preflight (`get_token()`)
3. Writes `output/manifest/run-metadata.json`
4. Iterates all 27 endpoint specs in registry order
5. For each type: list → detail → documentation + backup + manifest records
6. Writes crosslinks, API citations, gap report
7. Writes `output/manifest/manifest.json` and `manifest.csv`
8. Logs to `output/logs/export.log` and stdout

### Step 3 — Review output

| Path | Purpose |
|------|---------|
| `output/documentation/` | Human-readable Markdown per object |
| `output/backup/` | Raw JSON/XML payloads |
| `output/manifest/manifest.json` | Full inventory with SHA-256 checksums |
| `output/gaps/manual-workarounds.md` | API gaps and runtime errors |
| `output/documentation/crosslinks.md` | Policy ↔ script/package relationships |

See [Output Directory Index](output/index.md) for full artifact documentation.

### Step 4 — Interpret exit code

| Exit | Meaning |
|------|---------|
| `0` | All collectors succeeded |
| `1` | One or more list/detail requests failed (check logs and gaps) |
| `2` | Auth preflight failed — fix credentials or URL |

## RBAC Tuning with `--stop-on-401`

When your API client lacks Read privileges on some endpoints, the default behavior is to log the 401, record the error, and continue to the next object type.

To fail fast and identify the first missing privilege:

```bash
python scripts/run_full_export.py --output output --stop-on-401
```

On 401, the orchestrator:

1. Records the error
2. Adds the endpoint path to `missing_privileges_by_endpoint` in run metadata
3. Breaks out of the collection loop
4. Still writes manifest, gaps, and partial documentation for what was collected
5. Prints a privilege summary to stdout

Source: `jamf_exporter/orchestrator.py` lines 131–137, 149–156.

## Re-Running Exports

- Safe to re-run; documentation builder clears stale per-object `.md` files before writing new ones
- Backup files are overwritten per object ID
- Manifest is regenerated each run
- `output/` is gitignored — store exports outside the repo if you need version history

## Legacy Export Path (Not Recommended)

The older standalone script uses JSON config:

```bash
python src/jamf_audit_exporter.py \
  --config config/jamf_config.example.json \
  --catalog config/endpoint_catalog.json \
  --output output \
  --verbose
```

See [Legacy Tools](scripts/legacy-tools.md).

## Safe Restore (Scaffolding Only)

Non-dry-run restore requires `JAMF_ALLOW_RESTORE=true` and is not fully implemented in the production package:

```bash
python -m jamf_exporter.restore.safe_restore --manifest output/manifest/manifest.json --dry-run
```

See [Legacy Tools](scripts/legacy-tools.md) for the older `src/jamf_restore.py` dry-run/apply flow.
