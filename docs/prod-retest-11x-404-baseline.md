# Prod re-test — 11.x 404 baseline

Baseline: `Prod tests/JamfBackup-2026-06-02T192523Z` — **8 API 404s** in `logs/failures.json`.

## Run

1. Build or install a Jamf Dossier build that includes the 404 registry fixes.
2. Export against your Jamf Pro **11.x** server (same credentials as baseline).
3. Open `manifest/run-metadata.json` — confirm `jamf_pro_version` matches the UI.
4. Diff `logs/failures.json` against baseline paths.

## Expected

| Object type | Baseline path (404) | Expected after fix |
|-------------|---------------------|-------------------|
| check_in_settings | `/JSSResource/checkin` | 200 via `/api/v3/check-in` or `/computercheckin` |
| self_service_settings | `/JSSResource/selfservice` | 200 via `/api/v1/self-service/settings` |
| inventory_collection_settings | `/api/v1/inventory-collection-settings` | 200 via `/api/v1/computer-inventory-collection-settings` |
| jamf_pro_user_accounts | `/JSSResource/accounts/userid` | 200 via `/JSSResource/accounts` + detail |
| jamf_pro_user_groups | `/JSSResource/accounts/groupid` | 200 via `/JSSResource/accounts` + detail |
| app_installers | `/api/v1/app-installers` | 200 if feature + privilege; else 404 with privilege note |
| computer_prestages | `/api/v2/computer-prestages` | 200 if prestages exist; else 404 (empty/RBAC) |
| webhooks | `/api/v1/webhooks` | 200 if webhooks exist; else 404 (empty/RBAC) |

SSH failures (`database_backup`, `tomcat_config`) are unchanged by this work.

## One-line version check

```bash
curl -sS -H "Authorization: Bearer $TOKEN" "$JAMF_URL/api/v1/jamf-pro-version"
```
