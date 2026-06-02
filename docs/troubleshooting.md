# Troubleshooting

All items below are verified against source code and observed runtime behavior.

## Authentication Failures (Exit Code 2)

### Symptom

```
Authentication preflight failed: ...
Export aborted before collection.
```

Exit code `2`. No objects collected.

### Causes & Fixes

| Cause | Fix |
|-------|-----|
| Wrong `JAMF_URL` | Use exact Jamf Pro URL including port for on-prem (e.g. `https://host:8443`) |
| Placeholder credentials in `export.env` | Replace `YOUR_CLIENT_ID` etc. with real values |
| OAuth client disabled or wrong secret | Regenerate secret in Jamf Pro → API Roles and Clients |
| CloudFront 403 HTML response | Usually wrong URL or edge blocking; auth module detects HTML bodies and suggests URL check |
| No credentials at all | Set OAuth (`JAMF_CLIENT_ID` + `JAMF_CLIENT_SECRET`) or Basic (`JAMF_USERNAME` + `JAMF_PASSWORD`) |

Source: `jamf_exporter/auth.py`, `jamf_exporter/config.py` (`_reject_placeholders`).

## SSL Certificate Verify Failed

### Symptom

```
SSLError: certificate verify failed
```

### Fix

For self-signed on-prem certificates:

```bash
JAMF_VERIFY_TLS="false"
```

in `config/export.env`. Reload env before running.

## HTTP 401 on Specific Endpoints (Exit Code 1)

### Symptom

```
List request failed with status 401
```

Some object types export; others fail. Exit code `1`.

### Cause

API client or user lacks **Read** privilege for that Jamf object class.

### Fix

1. In Jamf Pro: **Settings → System Settings → API Roles and Clients**
2. Edit the role assigned to your API client
3. Grant **Read** for the failing resource (e.g. Categories, Extension Attributes)
4. Re-run export

Use `--stop-on-401` to identify the first failing endpoint:

```bash
python scripts/run_full_export.py --output output --stop-on-401
```

End-of-run summary lists `missing_privileges_by_endpoint`.

Source: `jamf_exporter/orchestrator.py` lines 149–156.

## urllib3 InsecureRequestWarning Flood

### Symptom

Repeated warnings when `JAMF_VERIFY_TLS=false`.

### Fix

Built-in: orchestrator filters these warnings when TLS verify is disabled. Ensure you are running the current `jamf_exporter` package, not legacy scripts.

Source: `jamf_exporter/orchestrator.py` lines 95–99.

## Duplicate or Placeholder Env Vars

### Symptom

Auth works in Jamf UI but fails in exporter; env shows placeholder values.

### Cause

Duplicate keys in `config/export.env` — last value wins, or placeholder block appended after real credentials.

### Fix

Remove duplicate entries; keep only one block of credentials.

## Empty Object Type / Zero Objects

### Symptom

`Total objects: 0` in type README but no error.

### Cause

Jamf instance has no objects of that type, or list response parsed to zero IDs.

### Verification

Check Jamf Pro UI for that object class. Inspect `output/logs/export.log` for list request status.

## XML/JSON Parse Errors

### Symptom

```
Unable to parse Classic API list XML response.
Unable to parse Jamf Pro API list JSON response.
```

### Cause

Unexpected response body (HTML error page, empty body, API version mismatch).

### Fix

1. Confirm API endpoint is available on your Jamf version
2. Check `output/logs/export.log` for raw status codes
3. Review `docs/endpoint-verification.md` for endpoint compatibility notes

Source: `jamf_exporter/collectors/generic.py` lines 69–87.

## Export Stops Early with `--stop-on-401`

### Expected Behavior

This is intentional. The flag stops at the first 401 to help RBAC debugging. Remove the flag for a best-effort full export of all accessible types.

## Manifest File Path Mismatch

### Symptom

Restore or external tools cannot find backup files.

### Note

`manifest.json` records `file_path` relative to the `--output` root (e.g. `output/backup/policies/1__Update_Inventory.xml`). Legacy `src/jamf_restore.py` resolves paths relative to manifest parent — behavior differs between tools.

## Getting Help

1. Check `output/logs/export.log`
2. Review `output/gaps/manual-workarounds.md`
3. Review `output/manifest/run-metadata.json` for `errors` and `missing_privileges_by_endpoint`
4. Run tests: `pytest -q`
