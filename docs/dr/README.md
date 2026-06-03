# Jamf DR backup and restore

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export captures configuration, inventory, binaries, and vaulted secrets — it is **not** a literal clone of your tenant. Existing devices require **re-enrollment** on a new server (see [device-re-enrollment.md](device-re-enrollment.md)).

## Bundle v2 layout

| Directory | Contents |
|-----------|----------|
| `backup/` | Config/metadata XML and JSON (v1 compatible) |
| `manifest/` | `manifest.json`, `dr-manifest.json`, run metadata |
| `inventory/` | Per-device JSON, FileVault CSV |
| `binaries/` | Package `.pkg` files |
| `server/` | MySQL dump, Tomcat conf, filesystem manifests |
| `secrets/` | `vault.enc`, `vault-index.json` (no cleartext secrets in index) |
| `gaps/` | Privilege and platform limits |
| `captures/` | Manual operator captures |
| `logs/` | Export/restore logs |
| `restore/` | Dry-run `preview.md`, live `id-map.json` |

## Quick start

```bash
# Standard metadata export (CLI)
set -a && source config/export.env && set +a
python scripts/run_full_export.py --output ./backup-run

# Full DR tiers (Python API — CLI flag not yet exposed)
python -c "
from pathlib import Path
from jamf_exporter import run_full_export
run_full_export(Path('./backup-run'), full_backup=True)
"

# Restore preview (no changes)
python -c "
from pathlib import Path
from jamf_exporter.config import RuntimeConfig
from jamf_exporter.restore.orchestrator import restore_from_bundle
restore_from_bundle(Path('./backup-run'), 'https://lab.jamf.example.com', RuntimeConfig.from_env(), dry_run=True)
"
```

For on-prem package binaries, also set `JAMF_SSH_HOST`, `JAMF_SSH_USER`, and optionally `JAMF_SSH_IDENTITY_FILE` before running with `full_backup=True`.

## Further reading

- [on-prem-mysql-restore.md](on-prem-mysql-restore.md)
- [cloud-api-restore.md](cloud-api-restore.md)
- [secrets-vault.md](secrets-vault.md)
- [device-re-enrollment.md](device-re-enrollment.md)
