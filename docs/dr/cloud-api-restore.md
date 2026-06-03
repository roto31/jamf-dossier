# Cloud API restore

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export is configuration + inventory + binaries + vaulted secrets — **not** a literal clone. Existing devices require **re-enrollment** on a new server.

Cloud export provides configuration, inventory snapshots, JCDS binaries (when on JCDS 2.0), and vaulted secrets. It does **not** reproduce MDM device trust or a literal copy of the tenant.

## Restore flow

1. Run dry-run: `restore/preview.md` lists object types in dependency order.
2. Target a **disposable lab** Jamf Cloud tenant first (`--lab-confirmed`).
3. Set `JAMF_ALLOW_RESTORE=true` and `JAMF_RESTORE_TARGET_URL`.
4. Restore config objects via Classic/Jamf Pro API writers.
5. Upload packages from `binaries/packages/` via JCDS or package upload APIs.
6. Inject secrets from `secrets/vault.enc` at restore time.
7. Plan **re-enrollment** for all managed devices (see [device-re-enrollment.md](device-re-enrollment.md)).

Never describe this path as a "clone."
