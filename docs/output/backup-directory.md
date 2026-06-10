# backup/ Directory

Raw API payloads for every successfully exported object.

## Structure

```
backup/
├── policies/
│   └── {id}__{name}.xml
├── scripts/
├── packages/
├── computer_groups/
└── ... (one subdirectory per exported object type)
```

Subdirectories appear only for types that returned at least one object.

## File naming

Pattern: `{jamf_id}__{safe_name}.{extension}`

| Extension | API family |
|-----------|------------|
| `.xml` | Classic API (policies, scripts, groups, …) |
| `.json` | Jamf Pro API |

## Use cases

- **Disaster recovery** — authoritative payload for restore planning
- **Diff / audit** — compare runs with checksums from `manifest/manifest.json`
- **Script review** — full `<script_contents>` in Classic API XML

## Related

- [documentation/](documentation-directory.md)
- [manifest, gaps, logs](manifest-gaps-logs.md)
- [Output Directory](index.md)
