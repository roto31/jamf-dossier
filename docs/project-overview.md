# Project Overview

## Purpose

**Jamf Settings Analysis** is a Python toolkit (with a companion Swift macOS app) that connects to a Jamf Pro instance, exports configuration objects via the Jamf Pro API and Classic API, and produces:

1. **Structured documentation** — Markdown files with extracted summaries and raw payloads
2. **Structured backups** — JSON or XML files suitable for audit, diff, and migration planning
3. **Operational metadata** — manifests with SHA-256 checksums, cross-link reports, gap analysis, DR bundle metadata, and run logs
4. **Optional DR tiers** — device inventory, package binaries, server filesystem probes, and encrypted secrets vault (when `full_backup=True`)

## Problem It Solves

Jamf Pro environments contain hundreds of interlinked objects (policies, groups, profiles, scripts, packages, prestages, LDAP, webhooks, etc.). Manual documentation is error-prone and quickly stale. This project automates:

- Full object enumeration by type (41 endpoint specs)
- Per-object export with checksums
- Cross-reference mapping (e.g., which policies use which scripts/packages)
- Identification of API gaps requiring manual UI capture
- DR bundle packaging for disaster-recovery planning

## Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3.10+ |
| HTTP client | `requests>=2.32.0` |
| Encryption (vault) | `cryptography>=42.0.0` (AES-256-GCM) |
| Packaging | setuptools (`pyproject.toml`) |
| Testing | pytest |
| Config | Environment variables (`config/export.env`) |
| Output formats | JSON, XML, Markdown, CSV |
| Native client | Swift/SwiftUI (`JamfBackup/`) |

## Repository Layout

```
Jamf-Settings-Analysis/
├── jamf_exporter/              # Primary production Python package
│   ├── collectors/             # Object and inventory collectors
│   ├── binaries/               # Package binary fetch (cloud/on-prem SSH)
│   ├── dr/                     # DR bundle manifest (v2.0)
│   ├── restore/                # Restore orchestration and writers
│   └── secrets/                # AES-256-GCM secrets vault
├── scripts/
│   └── run_full_export.py      # Main CLI
├── src/                        # Legacy standalone scripts
├── config/                     # Example env and endpoint catalog
├── docs/                       # This documentation wiki
├── tests/                      # Unit and integration tests
├── JamfBackup/                 # SwiftUI macOS backup app
├── jamf_mcp/                   # MCP server subproject
├── release_mcp/                # Release automation MCP
├── endpoint_registry.json      # Synced endpoint catalog (Python + Swift)
└── output/                     # Generated export artifacts (gitignored)
```

## Supported Jamf Object Types

The exporter iterates **41** endpoint specs defined in `jamf_exporter/endpoint_registry.py` (synced to `endpoint_registry.json`):

| Category | Object types |
|----------|--------------|
| Policies & payloads | `policies`, `scripts`, `packages`, `categories` |
| Groups & profiles | `computer_groups`, `mobile_device_groups`, `computer_configuration_profiles`, `mobile_device_configuration_profiles` |
| Apps | `mac_app_store_apps`, `mobile_device_apps`, `app_installers` |
| Searches & EAs | `computer_extension_attributes`, `advanced_computer_searches`, `advanced_mobile_device_searches` |
| Prestages | `computer_prestages`, `mobile_device_prestages` |
| Settings | `inventory_collection_settings`, `check_in_settings`, `self_service_settings`, `enrollment_settings`, `self_service_branding` |
| Org structure | `network_segments`, `buildings`, `departments`, `sites` |
| Accounts & integrations | `jamf_pro_user_accounts`, `jamf_pro_user_groups`, `webhooks`, `ldap_servers`, `api_integrations`, `api_roles` |
| Distribution & certs | `distribution_points`, `certificates`, `patch_software_titles` |
| VPP & SSO | `vpp_accounts`, `sso_settings` |
| Inventory (list endpoints) | `computers_inventory`, `computers_filevault`, `mobile_devices_inventory` |
| JCDS (cloud) | `jcds_files`, `jcds_file_url` |

Source: `endpoint_registry.json` → `endpoints` array (41 entries).

## DR Bundle v2.0

When `run_full_export(..., full_backup=True)` is used (Python API or tests; not yet exposed on CLI), the orchestrator creates additional bundle directories defined in `jamf_exporter/dr/schema.py`:

| Directory | Purpose |
|-----------|---------|
| `backup/` | Config/metadata XML and JSON |
| `manifest/` | Inventory, DR manifest, compatibility spec |
| `inventory/` | Per-device JSON, FileVault CSV |
| `binaries/` | Package `.pkg` files |
| `server/` | JSS filesystem cache manifest |
| `secrets/` | Encrypted vault (`vault.enc`, `vault-index.json`) |
| `gaps/` | Privilege and platform limit notes |
| `captures/` | Manual operator captures (reserved) |
| `logs/` | Export and failure logs |

See [DR Bundle Directories](output/dr-bundle-directories.md) and [DR Overview](dr/README.md).

## Known API Gaps

Two object types cannot be fully exported via public API endpoints. These are documented in:

- `jamf_exporter/endpoint_registry.py` → `get_manual_gaps()`
- `output/gaps/manual-workarounds.md` (generated at runtime)
- [manual-sso.md](manual-sso.md), [manual-ldap.md](manual-ldap.md), [manual-self-service.md](manual-self-service.md)

Static manual gaps (from registry):

| Object type | Reason |
|-------------|--------|
| `sso_configuration` | No stable public endpoint for complete SSO provider config across Jamf versions |
| `api_integrations_secrets` | Integration shared secrets are not retrievable in clear form |

## Version

Exporter version is recorded in `output/manifest/run-metadata.json` as `exporter_version` (currently `0.1.0` per `jamf_exporter/orchestrator.py` line 86).

DR bundle version is `2.0` per `jamf_exporter/dr/schema.py` → `BUNDLE_VERSION`.
