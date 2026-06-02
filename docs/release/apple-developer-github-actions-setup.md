# Apple Developer + GitHub Actions setup

## Prerequisites

- Apple Developer Program membership
- Admin on private repo `Jamf-Settings-Analysis`
- macOS with Xcode 15+ for one-time `.p12` export (optional; CI does the rest)

## Certificates

1. [Certificates portal](https://developer.apple.com/account/resources/certificates/list) → **Developer ID Application**
2. Export `.p12` from Keychain Access
3. Base64 for CI: `base64 -i cert.p12 | pbcopy` → `BUILD_CERTIFICATE_BASE64`

## Notarization (App Store Connect API key — preferred)

1. [App Store Connect API keys](https://appstoreconnect.apple.com/access/integrations/api)
2. Download `.p8` once → `ASC_KEY_P8_BASE64`
3. Set `ASC_KEY_ID`, `ASC_ISSUER_ID`, `NOTARY_TEAM_ID`

Alternative: `NOTARY_APPLE_ID` + app-specific password (`NOTARY_APP_PASSWORD`).

## GitHub secrets (private repo)

See [`github-secrets.template.env`](github-secrets.template.env).

**Interactive validate + upload (recommended):**

```bash
# Validate .p12 / .p8 and notarytool auth; prints checklist
bash scripts/setup_github_release_secrets.sh

# Same prompts, then `gh secret set` on the private repo
bash scripts/setup_github_release_secrets.sh --apply
```

Requires `gh auth login` with admin on `Jamf-Settings-Analysis` for `--apply`.

| Secret | Required for |
|--------|----------------|
| `BUILD_CERTIFICATE_BASE64`, `P12_PASSWORD`, `KEYCHAIN_PASSWORD` | Codesign |
| `ASC_*` or `NOTARY_*` | Notarization |
| `PUBLIC_REPO_TOKEN` | Public release upload |

## Build locally

```bash
bash scripts/release_build_macos_app.sh
# Output: dist/release/Jamf Dossier.app, dist/release/Jamf Dossier-0.1.0-macos.dmg
```

Without secrets, the script builds an unsigned `.app` and DMG.

## Trigger release

```bash
git tag -a v0.1.0 -m "First signed release"
git push origin v0.1.0
```

Workflow: [`.github/workflows/release.yml`](../../.github/workflows/release.yml) runs tests, [`scripts/release_build_macos_app.sh`](../../scripts/release_build_macos_app.sh), uploads artifacts, and optionally [`scripts/publish_public_release.sh`](../../scripts/publish_public_release.sh).

## Verify

```bash
spctl -a -vvv -t install "dist/release/Jamf Dossier.app"
```
