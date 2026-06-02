# Project Overview

## Purpose

**Jamf Settings Analysis** is a Python toolkit that connects to a Jamf Pro instance, exports configuration objects via the Jamf Pro API and Classic API, and produces:

1. **Structured documentation** — Markdown files with extracted summaries and raw payloads
2. **Structured backups** — JSON or XML files suitable for audit, diff, and migration planning
3. **Operational metadata** — manifests, cross-link reports, gap analysis, and run logs

## Problem It Solves

Jamf Pro environments contain hundreds of interlinked objects (policies, groups, profiles, scripts, packages, prestages, LDAP, webhooks, etc.). Manual documentation is error-prone and quickly stale. This project automates:

- Full object enumeration by type
- Per-object export with checksums
- Cross-reference mapping (e.g., which policies use which scripts/packages)
- Identification of API gaps requiring manual UI capture

## Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3.10+ |
| HTTP client | `requests` |
| Packaging | setuptools (`pyproject.toml`) |
| Testing | pytest |
| Config | Environment variables (`config/export.env`) |
| Output formats | JSON, XML, Markdown, CSV |

## Repository Layout

```
Jamf-Settings-Analysis/
├── jamf_exporter/          # Primary production package
├── scripts/
│   └── run_full_export.py  # Main CLI
├── src/                    # Legacy standalone scripts
├── config/                 # Example env and endpoint catalog
├── docs/                   # This documentation wiki
├── tests/                  # Unit tests
├── output/                 # Generated export artifacts (gitignored)
└── jamf_mcp/               # Separate MCP server subproject
```

## Supported Jamf Object Types

The exporter iterates 27 endpoint specs defined in `jamf_exporter/endpoint_registry.py`:

| Category | Object types |
|----------|--------------|
| Policies & payloads | `policies`, `scripts`, `packages`, `categories` |
| Groups & profiles | `computer_groups`, `mobile_device_groups`, `computer_configuration_profiles`, `mobile_device_configuration_profiles` |
| Apps | `mac_app_store_apps`, `mobile_device_apps`, `app_installers` |
| Searches & EAs | `computer_extension_attributes`, `advanced_computer_searches`, `advanced_mobile_device_searches` |
| Prestages | `computer_prestages`, `mobile_device_prestages` |
| Settings | `inventory_collection_settings`, `check_in_settings`, `self_service_settings` |
| Org structure | `network_segments`, `buildings`, `departments`, `sites` |
| Accounts & integrations | `jamf_pro_user_accounts`, `jamf_pro_user_groups`, `webhooks`, `ldap_servers` |

## Known API Gaps

Some object types cannot be fully exported via public API endpoints. These are documented in:

- `jamf_exporter/endpoint_registry.py` → `get_manual_gaps()`
- `output/gaps/manual-workarounds.md` (generated at runtime)
- [manual-sso.md](manual-sso.md), [manual-ldap.md](manual-ldap.md), [manual-self-service.md](manual-self-service.md)

## Version

Exporter version is recorded in `output/manifest/run-metadata.json` as `exporter_version` (currently `0.1.0` per `jamf_exporter/orchestrator.py`).
