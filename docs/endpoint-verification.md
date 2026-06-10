# Endpoint Verification

How Jamf Dossier validates export coverage against your Jamf Pro instance.

## RegistryFilter (v0.4.0)

Before collection, the export engine:

1. Probes Jamf Pro version and deployment mode
2. Applies `RegistryFilter` using probe results and registry flags (`cloud_only`, optional types)
3. Writes skipped endpoints to `gaps/skipped-endpoints.json`

Skipped endpoints are **expected** on many deployments — not export failures.

## Operator verification

| Check | Location |
|-------|----------|
| Object counts | `manifest/run-metadata.json` |
| Missing privileges | `missing_privileges_by_endpoint` in run metadata |
| Expected skips | `gaps/skipped-endpoints.json` |
| Actionable errors | `logs/failures.json` |

## Related

- [API Endpoint Citations](api-endpoint-citations.md)
- [Manifest, gaps, logs](output/manifest-gaps-logs.md)
- [Architecture](architecture.md)
