# Project Overview

**Jamf Dossier** is a macOS application that exports Jamf Pro configuration for audit, documentation, and disaster-recovery planning.

## What it does

1. **Structured backups** — JSON/XML per object in `backup/`
2. **Readable documentation** — Markdown with plain-text extractors in `documentation/`
3. **Operational metadata** — manifests, checksums, crosslinks, gap reports
4. **Optional DR tiers** — inventory, package binaries, server/MySQL capture (on-prem)

## Capabilities

| Feature | Description |
|---------|-------------|
| 41 object types | Bundled endpoint registry (Classic + Jamf Pro API) |
| RegistryFilter | Skips expected-unavailable endpoints; records skips separately from failures |
| Keychain auth | OAuth or Basic API credentials |
| DR Coverage UI | Shows which tiers apply to cloud vs on-prem |
| Plain-text docs | Script source, policy scope, smart group criteria |

## What it does not do

- Push backups to GitHub automatically
- Replace on-prem MySQL restore for full tenant clone
- Re-enroll devices after cloud restore (see [Device Re-Enrollment](dr/device-re-enrollment.md))

## Related

- [Getting Started](getting-started.md)
- [Architecture](architecture.md)
- [Export Engine](export-engine.md)
