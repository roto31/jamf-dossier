# Troubleshooting

All items verified against source code and observed runtime behavior.

## Authentication Failures (Exit Code 2)

### Symptom

```
Authentication preflight failed: ...
```

Exit code `2`. No objects collected (partial metadata/gaps still written).

### Causes & Fixes

| Cause | Fix |
|-------|-----|
| Wrong `JAMF_URL` | Use exact Jamf Pro URL including port for on-prem (e.g. `https://host:8443`) |
| Placeholder credentials | Replace `YOUR_CLIENT_ID` etc. with real values |
| Placeholder URL | `config.py` rejects URLs containing `your-`, `your_`, or `example` |
| OAuth client disabled or wrong secret | Regenerate secret in Jamf Pro → API Roles and Clients |
| CloudFront 403 HTML response | Wrong URL or edge blocking; auth module detects HTML and suggests URL check |
| No credentials | Set OAuth (`JAMF_CLIENT_ID` + `JAMF_CLIENT_SECRET`) or Basic (`JAMF_USERNAME` + `JAMF_PASSWORD`) |

Source: `jamf_exporter/auth.py`, `jamf_exporter/config.py` lines 32–35.

## SSL Certificate Verify Failed

### Symptom

```
SSLError: certificate verify failed
```

### Fix

```bash
JAMF_VERIFY_TLS="false"
```

in `config/export.env`. Reload env before running.

## HTTP 401 on Specific Endpoints (Exit Code 1)

### Symptom

Some object types export; others fail. Exit code `1`.

### Fix

1. Jamf Pro → **Settings → System Settings → API Roles and Clients**
2. Grant **Read** for the failing resource
3. Re-run export

```bash
python scripts/run_full_export.py --output output --stop-on-401
```

Check `manifest/run-metadata.json` → `missing_privileges_by_endpoint`.

Source: `jamf_exporter/orchestrator.py` lines 166–171, 241–245.

## urllib3 InsecureRequestWarning Flood

Built-in suppression when `JAMF_VERIFY_TLS=false`. Ensure you run the current `jamf_exporter` package.

Source: `jamf_exporter/orchestrator.py` lines 120–125.

## Duplicate or Placeholder Env Vars

Remove duplicate keys in `config/export.env`; last value wins.

## Empty Object Type / Zero Objects

Jamf instance may have no objects of that type, or list response parsed to zero IDs. Check Jamf Pro UI and `output/logs/export.log`.

## XML/JSON Parse Errors

```
Unable to parse Classic API list XML response.
Unable to parse Jamf Pro API list JSON response.
```

1. Confirm endpoint available on your Jamf version
2. Check `output/logs/export.log` for status codes
3. Review [endpoint-verification.md](endpoint-verification.md)

Source: `jamf_exporter/collectors/generic.py`.

## Export Stops Early with `--stop-on-401`

Expected behavior for RBAC debugging. Remove flag for best-effort full export.

## Package Binaries Not Downloaded (On-Prem)

### Symptom

`gaps/package-binaries-ssh-required.md` present; `binaries/packages/` empty.

### Cause

On-prem deployment without SSH credentials. Binary fetch requires SCP access to JSS package cache.

### Fix

Set `JAMF_SSH_HOST`, `JAMF_SSH_USER`, and optionally `JAMF_SSH_IDENTITY_FILE`. Run with `full_backup=True`.

Source: `jamf_exporter/binaries/package_fetcher.py`.

## Inventory Privilege Gaps

### Symptom

`gaps/computers-inventory-privilege-missing.md` or `gaps/filevault-privilege-missing.md`.

### Fix

Grant **Computers: Read** and (for FileVault) **View Disk Encryption Recovery Key** on the API role.

Source: `jamf_exporter/collectors/inventory.py`.

## Vault Encryption Errors

### Symptom

```
JAMF_DR_PASSPHRASE is required for vault encryption.
cryptography package is required for vault encryption.
```

### Fix

Set `JAMF_DR_PASSPHRASE` and ensure `pip install -e .` installed `cryptography`.

Source: `jamf_exporter/secrets/vault.py`.

## Restore Blocked

### Symptom

```
Set JAMF_ALLOW_RESTORE=true for non-dry-run restore.
Set JAMF_RESTORE_TARGET_URL for non-dry-run restore.
First real restore requires lab_confirmed=True.
```

### Fix

These are intentional safety gates. Use `dry_run=True` for preview. For live restore, set all required env vars and pass `lab_confirmed=True`.

Source: `jamf_exporter/restore/orchestrator.py` lines 17–38.

## Manifest File Path Mismatch

Production `manifest.json` uses `file_path` relative to output root. Legacy `src/jamf_restore.py` resolves paths differently — use production restore scaffolding instead.

## ModuleNotFoundError: requests

Activate venv and run `pip install -e .` before executing scripts.

## Export Parity Failures

```bash
python scripts/compare_export_outputs.py --python-only  # smoke test
python scripts/compare_export_outputs.py                # full parity
```

See [Export Parity Testing](testing/export-parity.md).

## Getting Help

1. `output/logs/export.log`
2. `output/logs/failures.json`
3. `output/gaps/manual-workarounds.md`
4. `output/manifest/run-metadata.json`
5. `pytest -q`
