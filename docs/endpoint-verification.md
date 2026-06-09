# Endpoint Verification

This project exports **41 cataloged Jamf API object types** (see `JamfBackup/.../endpoint_registry.json` and `jamf_exporter/endpoint_registry.py`). It does **not** export every Jamf Pro Settings UI field or every API endpoint.

Each export run records **`jamf_pro_version`** from `GET /api/v1/jamf-pro-version` in `manifest/run-metadata.json`.

## Official sources used

- Jamf Pro API developer resources: https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-developer-resources
- Jamf Pro API overview: https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-overview
- Bearer token auth endpoint: https://developer.jamf.com/jamf-pro/reference/post_v1-auth-token
- Classic API privilege mapping: https://developer.jamf.com/jamf-pro/docs/classic-api-minimum-required-privileges-and-endpoint-mapping
- Classic API overview: https://developer.jamf.com/jamf-pro/docs/getting-started-2
- Local instance Swagger: `https://YOUR_JAMF_URL/api` and `https://YOUR_JAMF_URL/api/doc`

## Jamf Pro 11.x path corrections (prod 404 baseline)

These registry paths were wrong or incomplete on Jamf Pro **11.x** (see prod run `JamfBackup-2026-06-02T192523Z`):

| Object type | Old list path (404) | Primary path (11.x) | Fallback |
|-------------|---------------------|---------------------|----------|
| check_in_settings | `/JSSResource/checkin` | `GET /api/v3/check-in` | `GET /JSSResource/computercheckin` |
| self_service_settings | `/JSSResource/selfservice` | `GET /api/v1/self-service/settings` | — |
| inventory_collection_settings | `/api/v1/inventory-collection-settings` | `GET /api/v1/computer-inventory-collection-settings` | legacy segment above |
| jamf_pro_user_accounts | `/JSSResource/accounts/userid` | `GET /JSSResource/accounts` → detail `.../userid/{id}` | `GET /api/v1/accounts` |
| jamf_pro_user_groups | `/JSSResource/accounts/groupid` | `GET /JSSResource/accounts` → detail `.../groupid/{id}` | `GET /api/v1/accounts` |

Paths unchanged but may be **skipped** as expected-unavailable (v0.4.0+) when probe or registry marks them N/A — not listed in `failures.json`:

| Object type | Path | Skip reason |
|-------------|------|-------------|
| app_installers | `/api/v1/app-installers` | Optional; 404 when feature unavailable |
| computer_prestages | `/api/v2/computer-prestages` | Optional; 404 when ADE prestages unavailable |
| webhooks | `/api/v1/webhooks` | Optional; probe 404 on some 11.x instances |
| certificates | `/JSSResource/certificates` | Optional; not on Jamf Pro 11.23.1 |
| jcds_files, jcds_file_url | `/api/v1/jcds/files` | Cloud-only; skipped on on-prem |

Use `--no-skip-probe` to force collection when debugging.

Validate any path on your server: `curl -H "Authorization: Bearer $TOKEN" "$JAMF_URL/api/v1/jamf-pro-version"` and compare to `/api/doc`.

## Verification approach

1. Each object type maps to endpoint metadata in `config/endpoint_catalog.json` and `endpoint_registry.json`.
2. `ObjectCollector` tries `list_path`, then `alternate_paths`, until HTTP status &lt; 400.
3. Runtime export logs include object type and resolved list endpoint.
4. Unsupported types are listed in `manual_gaps` and `gap-report.md`.

## Prod re-test checklist

After deploying a build with these fixes:

1. Run a full export against your Jamf Pro 11.x instance.
2. Confirm `manifest/run-metadata.json` contains `jamf_pro_version`.
3. Compare `logs/failures.json` — expect **zero** expected-unavailable entries on on-prem 11.x after v0.4.0 (see `gaps/skipped-endpoints.json` instead).
4. Prestages/webhooks/app-installers may appear under **Expected Unavailable** when the API is not exposed on your instance.
