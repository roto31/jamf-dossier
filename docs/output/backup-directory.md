# backup/ Directory

**Path:** `output/backup/`

Contains raw API payloads for every successfully exported object, organized by object type.

## Structure

```
output/backup/
├── policies/
│   ├── 1__Update_Inventory.xml
│   ├── 2__Chrome_Set-up.xml
│   └── ...
├── scripts/
│   └── {id}__{name}.xml
├── packages/
├── computer_groups/
├── computer_configuration_profiles/
├── mac_app_store_apps/
├── mobile_device_groups/
└── ... (one subdirectory per exported object_type)
```

Subdirectories are created only for object types that returned at least one object during the run.

## File Naming

Pattern: `{jamf_id}__{safe_name}.{extension}`

| Component | Rule |
|-----------|------|
| `jamf_id` | Jamf object ID from API |
| `safe_name` | Object name with non-alphanumeric chars replaced by `_`, max 120 chars |
| `extension` | `xml` for Classic API, `json` for Jamf Pro API |

Source: `jamf_exporter/backup_writer.py` → `_safe_name()`, `write_backups()`.

## File Formats

### Classic API (`.xml`)

Raw XML response from detail endpoint. Example object types:

- `policies`, `scripts`, `packages`, `computer_groups`, `mobile_device_groups`
- `computer_configuration_profiles`, `categories`, `buildings`, etc.

Content is the exact XML string stored in the collector's `raw_xml` field.

### Jamf Pro API (`.json`)

Pretty-printed JSON (indent=2, sorted keys) of the full object dict from the detail endpoint.

Example object types:

- `computer_prestages`, `mobile_device_prestages`
- `inventory_collection_settings`, `check_in_settings`
- `webhooks`, `ldap_servers`

## Production

| Step | Module |
|------|--------|
| Collect objects | `collectors/generic.py` |
| Write files + checksums | `backup_writer.py` |
| Register in manifest | `ExportRecord` appended per file |

## Manifest Cross-Reference

Each backup file has a corresponding row in `manifest/manifest.json`:

```json
{
  "object_type": "policies",
  "jamf_id": "1",
  "name": "Update Inventory",
  "source_endpoint": "/JSSResource/policies/id/{id}",
  "method": "GET",
  "output_format": "xml",
  "file_path": "output/backup/policies/1__Update_Inventory.xml",
  "checksum_sha256": "b6bd37cd7530c23264b502033ae7f54a410b724a4b668f963f349c34a1cb2488",
  "exported_at_utc": "2026-06-01T03:46:36.253141+00:00",
  "status": "success",
  "error": null
}
```

Use `checksum_sha256` to verify file integrity after copy or archival.

## Per-Type Subfolder Reference

Each subfolder name matches `EndpointSpec.object_type` from `endpoint_registry.py`:

| Subfolder | API Family | Format |
|-----------|------------|--------|
| `policies` | classic_api | xml |
| `scripts` | classic_api | xml |
| `packages` | classic_api | xml |
| `categories` | classic_api | xml |
| `computer_groups` | classic_api | xml |
| `mobile_device_groups` | classic_api | xml |
| `computer_configuration_profiles` | classic_api | xml |
| `mobile_device_configuration_profiles` | classic_api | xml |
| `mac_app_store_apps` | classic_api | xml |
| `mobile_device_apps` | classic_api | xml |
| `computer_extension_attributes` | classic_api | xml |
| `advanced_computer_searches` | classic_api | xml |
| `advanced_mobile_device_searches` | classic_api | xml |
| `buildings` | classic_api | xml |
| `departments` | classic_api | xml |
| `sites` | classic_api | xml |
| `network_segments` | classic_api | xml |
| `jamf_pro_user_accounts` | classic_api | xml |
| `jamf_pro_user_groups` | classic_api | xml |
| `computer_prestages` | jamf_pro_api | json |
| `mobile_device_prestages` | jamf_pro_api | json |
| `app_installers` | jamf_pro_api | json |
| `inventory_collection_settings` | jamf_pro_api | json |
| `self_service_settings` | jamf_pro_api | json |
| `check_in_settings` | jamf_pro_api | json |
| `webhooks` | jamf_pro_api | json |
| `ldap_servers` | jamf_pro_api | json |

If an object type fails at list or detail level (e.g. HTTP 401), its subfolder may be absent or contain fewer objects than exist in Jamf Pro.
