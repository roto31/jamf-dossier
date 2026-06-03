# Usage Guide

## Quick Start

```bash
cd Jamf-Settings-Analysis
source .venv/bin/activate
set -a && source config/export.env && set +a
python scripts/run_full_export.py --output output
python scripts/generate_output_indexes.py --output output
```

## CLI Reference

**Script:** `scripts/run_full_export.py`

| Flag | Default | Description |
|------|---------|-------------|
| `--output` | `output` | Root directory for all generated artifacts |
| `--stop-on-401` | off | Stop export immediately on first HTTP 401 (RBAC tuning) |

Source: `scripts/run_full_export.py` lines 19–28.

## End-to-End Walkthrough

### Step 1 — Configure credentials

Edit `config/export.env` with your Jamf URL and OAuth or Basic credentials. See [Setup & Installation](setup-installation.md).

### Step 2 — Run full export

```bash
python scripts/run_full_export.py --output output
```

The orchestrator:

1. Creates output directory and all DR bundle subdirectories (`BUNDLE_DIRS`)
2. Loads config from environment (or fixture session if `JAMF_FIXTURE_ROOT` set)
3. Runs auth preflight (`get_token()`)
4. Probes Jamf Pro version and deployment mode
5. Iterates all **41** endpoint specs in registry order
6. For each type: list → detail → documentation + backup + manifest records
7. Writes DR manifest, crosslinks, API citations, gap report, failure report, compatibility spec
8. Writes legacy mirrors (`docs/`, `raw/`, `manifests/`, `gap-report.md`)
9. Logs to `output/logs/export.log` and stdout

### Step 3 — Generate indexes (recommended)

```bash
python scripts/generate_output_indexes.py --output output
```

Creates README catalog files in every output subdirectory.

### Step 4 — Review output

| Path | Purpose |
|------|---------|
| `output/documentation/` | Human-readable Markdown per object |
| `output/backup/` | Raw JSON/XML payloads |
| `output/manifest/manifest.json` | Full inventory with SHA-256 checksums |
| `output/manifest/dr-manifest.json` | DR bundle metadata |
| `output/gaps/manual-workarounds.md` | API gaps and runtime errors |
| `output/documentation/crosslinks.md` | Policy ↔ script/package relationships |
| `output/logs/failures.json` | Structured failure report |

See [Output Directory Index](output/index.md) for complete artifact documentation.

### Step 5 — Interpret exit code

| Exit | Meaning |
|------|---------|
| `0` | All collectors succeeded |
| `1` | One or more list/detail requests failed (check logs and gaps) |
| `2` | Auth preflight failed — fix credentials or URL |

## Full Backup Tier (Python API)

The CLI does **not** expose `--full-backup`. To enable inventory, binaries, and server filesystem tiers, call the Python API directly:

```python
from pathlib import Path
from jamf_exporter import run_full_export

# Requires JAMF_SSH_HOST + JAMF_SSH_USER for on-prem package binaries
run_full_export(Path("output"), full_backup=True)
```

Or use the test harness pattern in `tests/test_export_parity_fixtures.py`.

When `full_backup=True`:

- `collect_inventory()` exports computers, mobile devices, FileVault keys
- `fetch_package_binaries()` downloads package `.pkg` files
- `fetch_jss_filesystem()` probes server filesystem (if SSH configured)
- `tiers_completed` in `dr-manifest.json` reflects completed tiers

See [DR Bundle Directories](output/dr-bundle-directories.md).

## RBAC Tuning with `--stop-on-401`

```bash
python scripts/run_full_export.py --output output --stop-on-401
```

On 401, the orchestrator records the error, breaks the collection loop, and still writes partial manifest/gaps/documentation.

Source: `jamf_exporter/orchestrator.py` lines 166–171.

## Fixture Mode (Offline Testing)

```bash
export JAMF_FIXTURE_ROOT="tests/fixtures/jamf"
export JAMF_URL="https://fixture.jamf.test"
export JAMF_CLIENT_ID="fixture-client"
export JAMF_CLIENT_SECRET="fixture-secret"
python scripts/run_full_export.py --output /tmp/fixture-export
```

Uses `fixture_transport.create_fixture_session()` instead of live HTTP.

## Publish Site Backup

```bash
python scripts/publish_lha_backup.py \
  --source output \
  --destination Lotus-Home-Academy-Backup \
  --site-name "Lotus Home Academy" \
  --jamf-url "https://lotushomeacademy.edu:8443"
```

See [Auxiliary Scripts](scripts/auxiliary-scripts.md).

## Restore Preview (Dry-Run)

```python
from pathlib import Path
from jamf_exporter.config import RuntimeConfig
from jamf_exporter.restore.orchestrator import restore_from_bundle

preview = restore_from_bundle(
    Path("output"),
    "https://lab.jamf.example.com",
    RuntimeConfig.from_env(),
    dry_run=True,
)
print(f"Preview written to {preview}")
```

Writes `output/restore/preview.md`. Live restore requires additional safety gates — see [DR Overview](dr/README.md).

## Re-Running Exports

- Safe to re-run; documentation builder clears stale per-object `.md` files
- Backup files overwritten per object ID
- Manifest regenerated each run
- `output/` is gitignored — archive exports outside the repo for version history

## Legacy Export Path (Not Recommended)

```bash
python src/jamf_audit_exporter.py \
  --config config/jamf_config.example.json \
  --catalog config/endpoint_catalog.json \
  --output output \
  --verbose
```

See [Legacy Tools](scripts/legacy-tools.md).

## Related

- [Setup & Installation](setup-installation.md)
- [Troubleshooting](troubleshooting.md)
- [Architecture](architecture.md)
