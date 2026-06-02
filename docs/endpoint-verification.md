# Endpoint Verification

This project exports **27 cataloged Jamf API object types** (see `jamf_exporter/endpoint_registry.py`). It does **not** export every Jamf Pro Settings UI field or every API endpoint.

This project references both Jamf Pro API and Classic API endpoints.

## Official sources used

- Jamf Pro API developer resources: https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-developer-resources
- Jamf Pro API overview: https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-overview
- Bearer token auth endpoint: https://developer.jamf.com/jamf-pro/reference/post_v1-auth-token
- Classic API overview: https://developer.jamf.com/jamf-pro/docs/getting-started-2
- Local instance Swagger and schema references (recommended by Jamf): `https://YOUR_JAMF_URL/api` and `https://YOUR_JAMF_URL/api/schema`

## Verification approach

1. Each object type maps to endpoint metadata in `config/endpoint_catalog.json`.
2. Each endpoint entry includes a Jamf developer reference URL.
3. Runtime export logs include object type and endpoint called.
4. Any unsupported object type is listed in `manual_gaps` and exported to `gap-report.md`.

## Notes

- Jamf changes endpoint availability by version. Always validate against your own Jamf instance Swagger docs at `/api` before production restore use.
- This exporter records failures and continues, so unsupported or missing endpoints are visible in output logs and manifests.
