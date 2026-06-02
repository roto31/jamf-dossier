# Jamf Settings Analysis — Documentation Wiki

Welcome to the project documentation. This wiki mirrors GitHub Wiki navigation using interlinked Markdown files in `docs/`.

## Start Here

| Page | Description |
|------|-------------|
| [Project Overview](project-overview.md) | Purpose, problem solved, tech stack |
| [Architecture](architecture.md) | System design, data flow, Mermaid diagrams |
| [Setup & Installation](setup-installation.md) | Prerequisites, venv, credentials |
| [Usage Guide](usage-guide.md) | End-to-end export walkthrough |
| [Troubleshooting](troubleshooting.md) | Verified fixes for common failures |

## Scripts & Code

| Page | Description |
|------|-------------|
| [run_full_export.py](scripts/run-full-export.md) | Primary CLI entrypoint |
| [jamf_exporter Package](scripts/jamf-exporter-package.md) | All production modules |
| [Legacy Tools](scripts/legacy-tools.md) | `src/jamf_audit_exporter.py`, `src/jamf_restore.py` |

## Output Artifacts

| Page | Description |
|------|-------------|
| [Output Directory Index](output/index.md) | Top-level `output/` layout |
| [backup/](output/backup-directory.md) | Raw JSON/XML object exports |
| [documentation/](output/documentation-directory.md) | Human-readable per-object docs |
| [manifest/, gaps/, logs/](output/manifest-gaps-logs.md) | Inventory, gaps, run logs |

## API & Manual Gaps

| Page | Description |
|------|-------------|
| [API Endpoint Citations](api-endpoint-citations.md) | Verified Jamf API references |
| [Endpoint Verification](endpoint-verification.md) | Validation approach |
| [Manual SSO Documentation](manual-sso.md) | SSO objects not fully API-exportable |
| [Manual LDAP Documentation](manual-ldap.md) | LDAP manual capture steps |
| [Manual Self Service Documentation](manual-self-service.md) | Self Service UI gaps |

## Related Subproject

| Page | Description |
|------|-------------|
| [jamf_mcp](../jamf_mcp/docs/INSTALLATION.md) | MCP server for Jamf API tooling (separate package) |

## Site Backups

| Page | Description |
|------|-------------|
| [Lotus Home Academy Wiki](lotus-home-academy/README.md) | Full backup catalog for Lotus Home Academy Jamf Pro |
| [Lotus backup folder](../Lotus-Home-Academy-Backup/README.md) | Raw backup artifacts in the repository |
