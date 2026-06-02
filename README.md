# Jamf Settings Analysis

Python toolkit for **full Jamf Pro environment documentation** and **structured configuration backup**.

Exports policies, groups, profiles, scripts, packages, prestages, LDAP, webhooks, and 20+ other object types via the Jamf Pro API and Classic API — producing Markdown documentation, raw JSON/XML backups, manifests with checksums, cross-link reports, and gap analysis.

## Quick Start

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -e .
cp config/export.example.env config/export.env
# Edit config/export.env with JAMF_URL and OAuth or Basic credentials

set -a && source config/export.env && set +a
python scripts/run_full_export.py --output output
```

## Documentation

Full project documentation lives in [`docs/`](docs/README.md) (Wiki-style navigation):

| Section | Link |
|---------|------|
| **Wiki home** | [docs/README.md](docs/README.md) |
| Project overview | [docs/project-overview.md](docs/project-overview.md) |
| Architecture & diagrams | [docs/architecture.md](docs/architecture.md) |
| Setup & installation | [docs/setup-installation.md](docs/setup-installation.md) |
| Usage guide | [docs/usage-guide.md](docs/usage-guide.md) |
| Troubleshooting | [docs/troubleshooting.md](docs/troubleshooting.md) |
| CLI reference | [docs/scripts/run-full-export.md](docs/scripts/run-full-export.md) |
| Package modules | [docs/scripts/jamf-exporter-package.md](docs/scripts/jamf-exporter-package.md) |
| Output artifacts | [docs/output/index.md](docs/output/index.md) |

## Authentication

Bearer tokens via:

1. OAuth2 client credentials (`POST /api/v1/oauth/token`) — preferred
2. Basic-to-token fallback (`POST /api/v1/auth/token`)

Credentials are read from environment variables in `config/export.env` (never committed).

## CLI Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--output` | `output` | User-selected output root for backups, generated documentation, manifests, and logs |
| `--stop-on-401` | off | Stop on first HTTP 401 (RBAC debugging) |

## Output Layout

```
output/
├── documentation/     # Per-object Markdown + crosslinks
├── backup/            # Raw JSON/XML by object type
├── manifest/          # manifest.json, manifest.csv, run-metadata.json
├── gaps/              # manual-workarounds.md
└── logs/              # export.log
```

Legacy-compatibility mirrors are also written when the exporter runs:

```
output/
├── docs/              # Aggregated legacy-style Markdown docs
├── raw/               # Legacy-style raw export mirror
├── manifests/         # Legacy manifest mirror
├── gap-report.md      # Legacy gap report path
└── logs/failures.json # Legacy failure report path
```

See [docs/output/index.md](docs/output/index.md) for complete artifact documentation.

## Tests

```bash
pytest -q
```

## API References

- [Jamf Pro API Overview](https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-overview)
- [Classic API Overview](https://developer.jamf.com/jamf-pro/docs/getting-started-2)
- [Endpoint citations](docs/api-endpoint-citations.md)

## macOS App (Jamf Backup)

Native SwiftUI backup client in [`JamfBackup/`](JamfBackup/README.md):

```bash
cd JamfBackup && swift build && swift run JamfBackup
```

Open `JamfBackup/Package.swift` in Xcode 26 for GUI runs and code signing.

## Related

- [jamf_mcp/](jamf_mcp/docs/INSTALLATION.md) — MCP server subproject for Jamf API tooling
