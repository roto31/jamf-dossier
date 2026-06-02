# Jamf API Endpoint Citations

This project verifies API usage against Jamf's official documentation and tenant-local API docs.

## Official Documentation Sources

- [Jamf Pro API Overview](https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-overview)
- [Classic API Overview](https://developer.jamf.com/jamf-pro/docs/getting-started-2)
- [Jamf Pro API Developer Resources](https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-developer-resources)
- [Classic API Authentication Changes](https://developer.jamf.com/jamf-pro/docs/classic-api-authentication-changes)
- Tenant-local Swagger/OpenAPI: `https://YOUR_JAMF_URL/api/doc`

## Authentication Endpoints

- `POST /api/v1/oauth/token` (OAuth2 client credentials): https://developer.jamf.com/jamf-pro/reference/postoauthtoken
- `POST /api/v1/auth/token` (basic-to-bearer token): https://developer.jamf.com/jamf-pro/reference/post_v1-auth-token

## Export Endpoint Mapping

The authoritative endpoint map used by code is in `jamf_exporter/endpoint_registry.py`, with one entry per object type including:

- API family
- list/detail paths
- method
- reference URL
- verification status
