# API Endpoint Citations

Jamf Dossier ships a bundled endpoint registry (`endpoint_registry.json`, 41 types). Each backup run also writes:

```
documentation/api-endpoint-citations.md
```

That file lists, per object type:

- API family (Classic vs Jamf Pro API)
- List and detail endpoint paths
- Official Jamf documentation reference URLs

Use it to validate RBAC requirements and Jamf version compatibility when troubleshooting 401/404 responses.

## Related

- [Export Engine](export-engine.md)
- [Endpoint Verification](endpoint-verification.md)
- [Troubleshooting](troubleshooting.md)
