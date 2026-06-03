# Jamf Settings Analysis — Documentation Wiki

Welcome to the project documentation. This wiki mirrors GitHub Wiki navigation using interlinked Markdown files in `docs/`.

## Start Here

| Page | Description |
|------|-------------|
| [Project Overview](project-overview.md) | Purpose, problem solved, tech stack, repository layout |
| [Architecture](architecture.md) | System design, data flow, Mermaid diagrams |
| [Setup & Installation](setup-installation.md) | Prerequisites, venv, credentials, optional DR/SSH vars |
| [Usage Guide](usage-guide.md) | End-to-end export walkthrough |
| [Troubleshooting](troubleshooting.md) | Verified fixes for common failures |

## Scripts & Code

| Page | Description |
|------|-------------|
| [run_full_export.py](scripts/run-full-export.md) | Primary CLI entrypoint |
| [jamf_exporter Package](scripts/jamf-exporter-package.md) | All production Python modules |
| [Auxiliary Scripts](scripts/auxiliary-scripts.md) | Index generation, wiki publish, parity compare, registry sync |
| [probe.py](scripts/probe.md) | Read-only backup feasibility probe |
| [Legacy Tools](scripts/legacy-tools.md) | `src/jamf_audit_exporter.py`, `src/jamf_restore.py` |

## Output Artifacts

| Page | Description |
|------|-------------|
| [Output Directory Index](output/index.md) | Top-level `output/` layout |
| [backup/](output/backup-directory.md) | Raw JSON/XML object exports |
| [documentation/](output/documentation-directory.md) | Human-readable per-object docs |
| [manifest/, gaps/, logs/](output/manifest-gaps-logs.md) | Inventory, gaps, run logs |
| [DR Bundle Directories](output/dr-bundle-directories.md) | `binaries/`, `inventory/`, `server/`, `secrets/`, `captures/` |

## Disaster Recovery

| Page | Description |
|------|-------------|
| [DR Overview](dr/README.md) | Bundle v2 layout and restore scope |
| [Secrets Vault](dr/secrets-vault.md) | AES-256-GCM encrypted secrets store |
| [Cloud API Restore](dr/cloud-api-restore.md) | API-based restore path |
| [On-Prem MySQL Restore](dr/on-prem-mysql-restore.md) | Database restore path |
| [Device Re-Enrollment](dr/device-re-enrollment.md) | Post-restore device handling |

## API & Manual Gaps

| Page | Description |
|------|-------------|
| [API Endpoint Citations](api-endpoint-citations.md) | Static endpoint reference (also generated at export time) |
| [Endpoint Verification](endpoint-verification.md) | Validation approach |
| [Manual SSO Documentation](manual-sso.md) | SSO objects not fully API-exportable |
| [Manual LDAP Documentation](manual-ldap.md) | LDAP manual capture steps |
| [Manual Self Service Documentation](manual-self-service.md) | Self Service UI gaps |

## Testing & Release

| Page | Description |
|------|-------------|
| [Export Parity Testing](testing/export-parity.md) | Python vs Swift fixture comparison |
| [Release Versioning](release-versioning.md) | SemVer and data revision policy |
| [Jamf Backup Operator Guide](jamf-backup-operator-guide.md) | Native macOS app operations |

## Related Subprojects

| Page | Description |
|------|-------------|
| [jamf_mcp](../jamf_mcp/docs/INSTALLATION.md) | MCP server for Jamf API tooling |
| [JamfBackup (Swift)](../JamfBackup/README.md) | Native SwiftUI backup client |
| [release_mcp](../release_mcp/docs/TOOLS.md) | Release automation MCP server |

## Site Backups

| Page | Description |
|------|-------------|
| [Lotus Home Academy Wiki](lotus-home-academy/README.md) | Full backup catalog for Lotus Home Academy Jamf Pro |
| [Lotus backup folder](../Lotus-Home-Academy-Backup/README.md) | Raw backup artifacts in the repository |
