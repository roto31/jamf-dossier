# Secrets Vault

AES-256-GCM encrypted secrets store for DR scenarios where API secrets cannot be exported in cleartext.

## Files

```
secrets/
├── vault.enc           # Encrypted blob
└── vault-index.json    # Key → entry UUID mapping (no cleartext values)
```

## Operator notes

- Passphrase is required to encrypt or decrypt the vault during export/restore workflows that use it
- Cleartext secrets never appear in `vault-index.json`
- Treat `vault.enc` and passphrase as sensitive backup material

## Related

- [DR Overview](README.md)
- [Cloud API Restore](cloud-api-restore.md)
