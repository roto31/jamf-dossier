# Jamf DR backup and restore

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export captures configuration, inventory, binaries, and vaulted secrets — it is **not** a literal clone of your tenant. Existing devices require **re-enrollment** on a new server.

## Bundle layout

| Directory | Contents |
|-----------|----------|
| `backup/` | Config/metadata XML and JSON |
| `manifest/` | `manifest.json`, `dr-manifest.json`, run metadata |
| `inventory/` | Per-device JSON, FileVault CSV |
| `binaries/` | Package `.pkg` files |
| `server/` | MySQL dump, Tomcat conf, filesystem manifests |
| `secrets/` | Encrypted vault |
| `gaps/` | Skips, privilege limits |
| `logs/` | Export logs |

Enable optional tiers in **Jamf Dossier Settings** (DR Coverage).

## Further reading

- [On-Prem MySQL Restore](on-prem-mysql-restore.md)
- [Cloud API Restore](cloud-api-restore.md)
- [Secrets Vault](secrets-vault.md)
- [Device Re-Enrollment](device-re-enrollment.md)
- [DR Bundle Directories](../output/dr-bundle-directories.md)
