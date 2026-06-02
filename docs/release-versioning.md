# Release versioning

## SemVer

- Tags: `vMAJOR.MINOR.PATCH` (e.g. `v0.1.0`)
- Python: `pyproject.toml` `[project].version`
- Swift / app: root `VERSION` file (read by export manifest)

## Folders

| Path | Git | Purpose |
|------|-----|---------|
| `build/` | Ignored | Local/CI compile scratch |
| `dist/` | Selective manifests | Checksums, build metadata per version |
| `release/` | Yes (metadata) | Per-tag notes and `artifact-manifest.json` |

Binaries (.app, .dmg) live on **GitHub Releases**, not in git.

## Changelog

Update `CHANGELOG.md` (Keep a Changelog) before tagging.
