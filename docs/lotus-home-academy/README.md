# Lotus Home Academy — Backup Documentation Wiki

Wiki-style documentation for the Jamf Pro backup in [`Lotus-Home-Academy-Backup/`](/Lotus-Home-Academy-Backup/README.md).

## Start Here

| Page | Description |
|------|-------------|
| [Site Overview](site-overview.md) | Jamf server context and backup purpose |
| [Snapshot Metadata](snapshot-metadata.md) | Export date, counts, completeness |
| [Jamf Object Architecture](jamf-object-architecture.md) | How object types relate on this server |

## Object Types

| Type | Count | Wiki Page |
|------|-------|-----------|
| `computer_configuration_profiles` | 9 | [Catalog](object-types/computer-configuration-profiles.md) |
| `computer_groups` | 21 | [Catalog](object-types/computer-groups.md) |
| `mac_app_store_apps` | 7 | [Catalog](object-types/mac-app-store-apps.md) |
| `mobile_device_groups` | 3 | [Catalog](object-types/mobile-device-groups.md) |
| `packages` | 24 | [Catalog](object-types/packages.md) |
| `policies` | 72 | [Catalog](object-types/policies.md) |
| `scripts` | 42 | [Catalog](object-types/scripts.md) |

## Artifacts & Relationships

| Page | Description |
|------|-------------|
| [Backup Payloads](artifacts/backup-payloads.md) | Raw XML/JSON files |
| [Documentation Files](artifacts/documentation-files.md) | Generated Markdown docs |
| [Manifest, Gaps & Logs](artifacts/manifest-gaps-logs.md) | Inventory and export errors |
| [Policy Crosslinks](relationships/crosslinks.md) | Script/package → policy map |

## Quick Facts

- **Jamf Pro URL:** `https://lotushomeacademy.edu:8443`
- **Exported at (UTC):** `2026-06-01T03:47:03.521931+00:00`
- **Total objects:** 178

See also: [Project documentation wiki](../README.md)
