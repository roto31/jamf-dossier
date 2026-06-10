# documentation/ Directory

Human-readable Markdown generated from exported Jamf objects.

## Structure

```
documentation/
├── api-endpoint-citations.md
├── crosslinks.md
├── policies/
│   ├── README.md
│   └── {id}__{name}.md
├── scripts/
└── ... (one subdirectory per exported type)
```

## Per-object pages

Each `{id}__{name}.md` may include:

| Section | Content |
|---------|---------|
| Extracted Summary | JSON metadata |
| Plain Text | Script source, policy scope, group criteria prose |
| Diagrams | Mermaid (policies) |
| Raw Object | Full XML/JSON for restore parity |

## Root files

- **crosslinks.md** — scripts/packages ↔ policies
- **api-endpoint-citations.md** — Jamf API reference links per type

## Related

- [backup/](backup-directory.md)
- [Export Engine](../export-engine.md)
- [Output Directory](index.md)
