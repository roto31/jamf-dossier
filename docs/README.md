# Jamf Dossier — Documentation

User documentation for the **Jamf Dossier** macOS application. This mirrors the [GitHub Wiki](https://github.com/roto31/jamf-dossier/wiki) navigation.

## Start here

| Page | Description |
|------|-------------|
| [Getting Started](getting-started.md) | Install DMG, first backup, Keychain setup |
| [Operator Guide](jamf-dossier-operator-guide.md) | Settings, on-prem MySQL/Tomcat, DR tiers |
| [Project Overview](project-overview.md) | Purpose, capabilities, tech stack |
| [Architecture](architecture.md) | App design and data flow (Mermaid) |
| [Setup](setup-installation.md) | System requirements and installation |
| [Usage Guide](usage-guide.md) | End-to-end backup walkthrough |
| [Troubleshooting](troubleshooting.md) | Common errors and fixes |

## Backup output

| Page | Description |
|------|-------------|
| [Output Directory Index](output/index.md) | Top-level backup folder layout |
| [backup/](output/backup-directory.md) | Raw JSON/XML exports |
| [documentation/](output/documentation-directory.md) | Markdown per-object docs |
| [manifest/, gaps/, logs/](output/manifest-gaps-logs.md) | Inventory and run metadata |
| [DR Bundle Directories](output/dr-bundle-directories.md) | inventory, binaries, server, secrets |

## Disaster recovery

| Page | Description |
|------|-------------|
| [DR Overview](dr/README.md) | Bundle v2 layout and restore scope |
| [Secrets Vault](dr/secrets-vault.md) | Encrypted secrets in backups |
| [Cloud API Restore](dr/cloud-api-restore.md) | API-based restore path |
| [On-Prem MySQL Restore](dr/on-prem-mysql-restore.md) | Database restore path |
| [Device Re-Enrollment](dr/device-re-enrollment.md) | Post-restore devices |

## Export engine

| Page | Description |
|------|-------------|
| [Export Engine](export-engine.md) | How Jamf Dossier collects and writes backups |
| [API Endpoint Citations](api-endpoint-citations.md) | Jamf API references per object type |
| [Endpoint Verification](endpoint-verification.md) | Coverage validation approach |

## Manual capture (API gaps)

| Page | Description |
|------|-------------|
| [Manual SSO](manual-sso.md) | SSO settings not fully API-exportable |
| [Manual LDAP](manual-ldap.md) | LDAP manual capture |
| [Manual Self Service](manual-self-service.md) | Self Service UI gaps |

## Legal

| Page | Description |
|------|-------------|
| [Proprietary Notice](legal/proprietary-notice.md) | License and distribution terms |

## Releases

| Resource | Link |
|----------|------|
| Changelog | [CHANGELOG.md](../CHANGELOG.md) |
| Release artifacts | [release/README.md](../release/README.md) |
