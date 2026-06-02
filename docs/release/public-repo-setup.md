# Public repository setup

## Create repo

1. Public repo: [`roto31/jamf-dossier`](https://github.com/roto31/jamf-dossier) (or set `PUBLIC_REPO` variable).
2. Add proprietary notice from [`docs/legal/proprietary-notice.md`](../legal/proprietary-notice.md).
3. Do **not** push `JamfBackup/` or `jamf_exporter/` source.

## Token

Create a fine-grained or classic PAT with `contents: write` on the public repo only. Store as `PUBLIC_REPO_TOKEN` in the **private** repo secrets.

## Dry run (local)

```bash
export PUBLIC_REPO_TOKEN=ghp_...
bash scripts/sync_public_docs.sh /path/to/jamf-dossier-clone
```

## Release publish

Tag `v*` on private repo runs [`release.yml`](../../.github/workflows/release.yml), which calls [`scripts/publish_public_release.sh`](../../scripts/publish_public_release.sh).
