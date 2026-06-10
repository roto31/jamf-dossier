# Manual LDAP Documentation

LDAP server bindings may require manual verification beyond API export.

## Manual capture

1. Jamf Pro → **Settings → System Settings → LDAP Servers**
2. Confirm exported `ldap_servers` objects in `backup/` match UI
3. Document bind DN, search bases, and group mappings not returned by API
4. Store sensitive bind passwords in operator vault — not in backup cleartext

## Related

- [backup/ directory](../output/backup-directory.md)
- [Manual SSO](manual-sso.md)
