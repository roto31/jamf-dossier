# CI platform comparison

| Platform | macOS builds | Fit |
|----------|--------------|-----|
| GitHub Actions | Yes (`macos-*`) | **Recommended** — native Releases |
| Jenkins (self-hosted Mac) | Yes | Ops overhead |
| Xcode Cloud | Yes | Apple-native; separate from GitHub UI |

Free Jenkins does not provide hosted macOS signing. This repo uses GitHub Actions.
