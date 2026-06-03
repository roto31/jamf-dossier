# Secrets vault

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export is configuration + inventory + binaries + vaulted secrets — **not** a literal clone. Existing devices require **re-enrollment** on a new server.

Jamf never returns webhook `client_secret`, LDAP bind passwords, API integration secrets, or private keys in cleartext. The DR vault captures operator-supplied values once at backup time.

## Python (CLI)

```bash
export JAMF_DR_PASSPHRASE='your-backup-passphrase'
python3 -c "
from pathlib import Path
from jamf_exporter.secrets.vault import Vault
v = Vault(Path('.'))
v.put('webhook:3:client_secret', 's3cr3t')
v.save(Path('.'))
"
```

Outputs:

- `secrets/vault.enc` — salt + nonce + AES-256-GCM ciphertext
- `secrets/vault-index.json` — maps `object_type:id:field` → entry UUID (**no secret values**)

## Swift (Jamf Dossier)

Use **Secrets capture** before backup. Passphrase is stored in Keychain (`jamf.dr_passphrase`) or `JAMF_DR_PASSPHRASE`.

## Restore

Decrypt vault on the restore workstation only. Inject secrets during webhook/LDAP restore steps. Never commit passphrases or vault files to git.
