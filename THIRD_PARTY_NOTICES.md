# Third-Party Notices

Jamf Dossier (`com.jamfsettingsanalysis.JamfDossier`) and the documentation and release metadata in this repository are licensed under the [MIT License](LICENSE).

Third-party components listed below remain under their own licenses. The project's MIT License does not replace, modify, or relicense any third-party component.

- **Date verified:** 2026-10-14
- **Scope:** release builds published from this repository's [GitHub Releases](https://github.com/roto31/jamf-dossier/releases). Versions below are those pinned for **v0.5.3** (latest release). Third-party Swift packages were added in v0.5.0; the v0.4.0 binary tracked under `dist/release/` links only Apple system frameworks and contains none of the components below.
- **Method:** each license was read from the upstream repository at the exact commit pinned in the build's `Package.resolved` (tag verified to resolve to the pinned revision). Presence in the shipped binary was confirmed by symbol inspection of the v0.5.3 `Jamf Dossier.app` executable.
- Apple system frameworks (SwiftUI, AppKit, Foundation, Security, CryptoKit, Swift runtime) are dynamically linked from macOS and are not redistributed.

## 1. Bundled / compiled components

Statically compiled into the `Jamf Dossier.app` executable distributed in the release DMG. License and NOTICE texts are shipped verbatim in [`third_party_licenses/`](third_party_licenses/).

| Package | Version | License (SPDX) | Source URL | Files in third_party_licenses/ |
|---------|---------|----------------|------------|--------------------------------|
| BigInt | 5.7.0 | MIT | https://github.com/attaswift/BigInt | `BigInt-LICENSE.txt` |
| Citadel | 0.12.1 | MIT | https://github.com/orlandos-nl/Citadel | `Citadel-LICENSE.txt` |
| Citadel — vendored OpenBSD `bcrypt.c` (`CCitadelBcrypt`) | in Citadel 0.12.1 | ISC | https://github.com/orlandos-nl/Citadel/tree/0.12.1/Sources/CCitadelBcrypt | `Citadel-bcrypt-LICENSE.txt` (verbatim source header) |
| Citadel — vendored OpenBSD `blf.c` / `blf.h` Blowfish (`CCitadelBcrypt`) | in Citadel 0.12.1 | BSD-4-Clause | https://github.com/orlandos-nl/Citadel/tree/0.12.1/Sources/CCitadelBcrypt | `Citadel-blf-LICENSE.txt` (verbatim source header) |
| Citadel — vendored `bcrypt-kdf.c` (`CCitadelBcrypt`) | in Citadel 0.12.1 | UNVERIFIED (no license header or copyright line in file) | https://github.com/orlandos-nl/Citadel/tree/0.12.1/Sources/CCitadelBcrypt | — |
| swift-asn1 | 1.7.1 | Apache-2.0 | https://github.com/apple/swift-asn1 | `swift-asn1-LICENSE.txt`, `swift-asn1-NOTICE.txt` |
| swift-atomics | 1.3.0 | Apache-2.0 WITH Swift-exception | https://github.com/apple/swift-atomics | `swift-atomics-LICENSE.txt` |
| swift-collections | 1.6.0 | Apache-2.0 WITH Swift-exception | https://github.com/apple/swift-collections | `swift-collections-LICENSE.txt` |
| swift-crypto | 3.15.1 | Apache-2.0 | https://github.com/apple/swift-crypto | `swift-crypto-LICENSE.txt`, `swift-crypto-NOTICE.txt` |
| swift-crypto — vendored BoringSSL (`CCryptoBoringSSL`) | revision `0226f30467f540a3f62ef48d453f93927da199b6` (in swift-crypto 3.15.1) | Apache-2.0 | https://boringssl.googlesource.com/boringssl (mirror: https://github.com/google/boringssl) | `BoringSSL-LICENSE.txt` |
| swift-crypto — vendored fiat-crypto generated code (`CCryptoBoringSSL/third_party/fiat`) | in swift-crypto 3.15.1 | UNVERIFIED (files carry no license text at pinned revision; upstream https://github.com/mit-plv/fiat-crypto ships `LICENSE-MIT`, `LICENSE-APACHE`, `LICENSE-BSD-1`) | https://github.com/apple/swift-crypto/tree/3.15.1/Sources/CCryptoBoringSSL/third_party/fiat | — |
| swift-log | 1.13.2 | Apache-2.0 | https://github.com/apple/swift-log | `swift-log-LICENSE.txt`, `swift-log-NOTICE.txt` |
| swift-nio | 2.101.0 | Apache-2.0 | https://github.com/apple/swift-nio | `swift-nio-LICENSE.txt`, `swift-nio-NOTICE.txt` |
| swift-nio-ssh (fork) | 0.3.6 | Apache-2.0 | https://github.com/Wellz26/swift-nio-ssh (fork; upstream https://github.com/apple/swift-nio-ssh) | `swift-nio-ssh-LICENSE.txt` |
| swift-system | 1.6.5 | Apache-2.0 WITH Swift-exception | https://github.com/apple/swift-system | `swift-system-LICENSE.txt` |

Notes:

- `swift-nio-ssh` is resolved from a personal fork because Citadel 0.12.1's own `Package.swift` depends on `https://github.com/Wellz26/swift-nio-ssh.git` (a fork of `Joannis/swift-nio-ssh`, itself a fork of `apple/swift-nio-ssh`). Its `LICENSE.txt` is byte-identical to `apple/swift-nio-ssh`. The fork has no `NOTICE.txt`; upstream `apple/swift-nio-ssh` also has none.
- BoringSSL's `LICENSE` also contains the Go license for parts of its TLS test suite; per that file, the test code is not compiled into BoringSSL.
- swift-nio's vendored `llhttp` and FreeBSD `sha1` sources have no symbols in the release binary, so they are not listed as shipped.
- BoringSSL's public-domain `crypto/poly1305/poly1305_arm_asm.S` compiles only for 32-bit ARM Linux and is not part of the macOS binary.

## 2. User-installed components

None. The release DMG is self-contained; users do not install additional packages from a registry to run Jamf Dossier.

## 3. Development / test only

None in this repository. This repository contains documentation, release metadata, and release artifacts only; build and test tooling for the application is not distributed from here.

## 4. Copyleft check

- **GPL / AGPL / LGPL / MPL:** none found among bundled components.
- **BSD-4-Clause (not copyleft, but has an advertising clause):** Citadel's vendored `blf.c` — bundled. Advertising materials that mention features or use of this software must display: "This product includes software developed by Niels Provos."
- **UNVERIFIED:** Citadel `bcrypt-kdf.c` and swift-crypto's vendored fiat-crypto files carry no license text at the pinned revisions.

## 5. Trademarks

Jamf Dossier is an independent project. It is not affiliated with, endorsed by, or sponsored by Jamf, Apple Inc., GitHub, Oracle, or the Apache Software Foundation.

- Jamf and Jamf Pro are trademarks of Jamf Software, LLC.
- Apple, macOS, and Keychain are trademarks of Apple Inc.
- GitHub is a trademark of GitHub, Inc.
- MySQL is a trademark of Oracle and/or its affiliates.
- Apache Tomcat is a trademark of the Apache Software Foundation.

Third-party names are used only to identify compatible products and services. Neither the MIT License nor any third-party license grants rights to use these trademarks. See [docs/legal/license-notice.md](docs/legal/license-notice.md).
