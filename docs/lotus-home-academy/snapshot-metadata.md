# Snapshot Metadata

Export run statistics for this backup. Source: [`run-metadata.json`](/Lotus-Home-Academy-Backup/manifest/run-metadata.json).

| Field | Value |
|-------|-------|
| Exported at (UTC) | `2026-06-01T03:47:03.521931+00:00` |
| Exporter version | `0.1.0` |
| Total objects | `178` |
| Error count | `2` |

## Object Counts

| Object Type | Count |
|-------------|-------|
| `computer_configuration_profiles` | 9 |
| `computer_groups` | 21 |
| `mac_app_store_apps` | 7 |
| `mobile_device_groups` | 3 |
| `packages` | 24 |
| `policies` | 72 |
| `scripts` | 42 |

## Completeness Notes

### Missing API Privileges (401)

- `/JSSResource/categories`: categories

### Runtime Export Errors

From [`manual-workarounds.md`](/Lotus-Home-Academy-Backup/gaps/manual-workarounds.md):

- `app_installers` `/api/v1/app-installers`: List request failed with status 404
- `categories` `/JSSResource/categories`: List request failed with status 401

Object types not present in this snapshot were either not exported due to errors or do not exist on the server.

## Related

- [Manifest, Gaps & Logs](artifacts/manifest-gaps-logs.md)
- [Site Overview](site-overview.md)
