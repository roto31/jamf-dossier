# On-prem MySQL restore

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export is configuration + inventory + binaries + vaulted secrets — **not** a literal clone. Existing devices require **re-enrollment** on a new server.

Use this path when you have a `server/database/jamf_*.sql.gz` dump from Server Tools SSH backup.

1. Install a fresh Jamf Pro instance at the **same major version** as the source (see `manifest/dr-manifest.json`).
2. Stop Tomcat on the target host.
3. Restore the MySQL dump per Jamf migration documentation.
4. Restore Tomcat files from `server/tomcat/` if captured.
5. Reconcile package binaries under the JSS package cache from `binaries/packages/`.
6. Re-enter secrets from the encrypted vault (LDAP bind, webhook client secrets).
7. Validate MDM/APNs trust and plan device re-enrollment where required.

MySQL restore preserves device rows and escrowed relationships better than API-only restore.
