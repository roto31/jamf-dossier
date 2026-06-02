# Export parity testing

## Fixture parity (every PR)

Uses canned HTTP responses under `tests/fixtures/jamf/`.

```bash
export JAMF_URL=https://fixture.jamf.test
export JAMF_CLIENT_ID=fixture-client
export JAMF_CLIENT_SECRET=fixture-secret
python scripts/compare_export_outputs.py
```

## Recording fixtures (one-time)

Capture from a test tenant with redacted secrets only. Name files using `METHOD__path_segments` (see `jamf_exporter/fixture_transport.py`).

## Live parity (optional)

Workflow `.github/workflows/parity-live.yml` requires secrets:

- `JAMF_URL`
- `JAMF_CLIENT_ID`
- `JAMF_CLIENT_SECRET`

Trigger manually or on weekly schedule.
