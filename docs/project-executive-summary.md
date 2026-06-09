# Jamf Dossier — Executive Project Status

**As of:** 2026-06-08 (v0.4.0 gap closure)  
**Version:** `0.4.0`  
**Private `main`:** ahead of `v0.2.0` with DR closeout engineering  
**Public release (prior):** [jamf-dossier v0.2.0](https://github.com/roto31/jamf-dossier/releases/tag/v0.2.0)  
**Related:** [project-status.md](project-status.md) (legacy checklist — points here)

---

## Bottom line

| Lens | Complete | Remaining | Score |
|------|----------|-----------|-------|
| **Shippable product (config backup + release pipeline)** | CI, signed DMG, public publish, macOS app, v0.4.0 feature set | Operator smoke on prod + lab live drill sign-off | **~97%** |
| **Bundle v2 DR platform (6-phase plan)** | Schema, registry sync, vault, inventory (Python + Swift), restore writers, UI wiring, harness | Live restore lab drill, FileVault privilege | **~88%** |
| **Full-content backup on your Jamf instance** | Tier-A, mobile inventory, computers/API roles/distribution points (post–re-probe) | FileVault bulk (PARTIAL), SSH package binaries in production run | **~78%** |
| **Automated disaster recovery (restore to new server)** | Dry-run preview, safety gates, live writer implementations (gated) | Non–dry-run lab validation, vault inject on real target | **~72%** |

**Overall program completion** (weighted: 40% shipping, 35% DR engineering, 25% operator/runtime): **~88% complete, ~12% remaining.**

---

## Closeout plan — remaining items (from ~28% gap)

| Gap | Weight (plan) | Status | **Done %** | **Remaining %** |
|-----|---------------|--------|------------|-----------------|
| API role privileges | ~5% | Re-probe committed; **36/41** registry verified; FileVault + API integrations **PARTIAL** | **85%** | **15%** |
| Wire DR UI | ~5% | Settings: tier toggles + secrets wizard; DR Coverage: restore wizard | **100%** | **0%** |
| Swift DR tier parity | ~8% | `InventoryCollector`, package SCP, `DRBundleLayout`, orchestrator `--full` path | **95%** | **5%** |
| Live restore writers | ~7% | `classic_put_post`, `jpapi_post`, `package_upload`, `inventory_import`, `vault_inject`; gates unchanged | **90%** | **10%** |
| Operator validation | ~3% | `operator_smoke.sh`, `dr-live-drill.yml`; no documented prod/lab run | **45%** | **55%** |
| Doc drift | (risk) | 41-type messaging; R7 scope on all `docs/dr/*.md` | **100%** | **0%** |
| Ship v0.4.0 (UAT → promote) | release | **v0.4.0** gap closure tagged; public DMG via release workflow | **in progress** | promote after smoke |

**Codeable closeout (AUTO): ~97% complete.** **Operator-only: ~35% remaining** (privilege fine-tuning + live smoke).

---

## What shipped since v0.2.0 (v0.3.0 scope)

- **Code:** Restore writers (POST/PUT/upload/vault-inject behind existing gates), Swift inventory + package binaries, DR UI wired, `sync_registry_from_probe.py`, restore writer tests, Bundle v2 parity test
- **Data:** Registry **36/41** probe-verified (up from 33); re-probe after privilege grants
- **Build/CI:** `dr-live-drill.yml` (manual, lab-only), `operator_smoke.sh`, public release **prerelease** by default

---

## Six-phase DR plan status (revised)

| Phase | Scope | **Complete** | **Remaining** |
|-------|--------|--------------|---------------|
| **0** | Feasibility probe | **100%** | Re-probe when privileges change |
| **1** | Schema, registry, vault, inventory | **~95%** | FileVault CSV until recovery-key privilege |
| **2** | Package binaries & assets | **~80%** | Production SSH run; Self Service asset polish |
| **3** | On-prem server (SSH) | **~80%** | Consistent `server/` layout in all runs |
| **4** | Restore engine (API) | **~85%** | Lab non–dry-run validation only |
| **5** | Swift UI + CLI parity | **~95%** | Minor branding/asset gaps |
| **6** | Tests, docs, validation | **~90%** | Operator smoke sign-off |

**Phases 1–6 average: ~88% complete / ~12% remaining**

---

## Success criteria (DR plan)

| # | Criterion | **Complete** |
|---|-----------|--------------|
| 1 | Bundle v2 with metadata + inventory + binaries + FileVault + secrets + MySQL | **~80%** — structure + collectors; prod `--full` run pending |
| 2 | Restore dry-run → accurate `preview.md` | **~95%** — Python + Swift preview builders |
| 3 | Honest `gaps/` reporting | **~98%** |
| 4 | Python CLI ↔ Jamf Dossier compatible Bundle v2 | **~90%** — Swift emits same top-level dirs as Python |
| 5 | `probe-report.json` + registry reflects probe | **~95%** — 36 verified; 5 platform/privilege limits documented |

**Success criteria average: ~92% complete / ~8% remaining**

---

## Content retrievability (on-prem probe, Jamf 11.23.1)

Source: [`probe-report.json`](../probe-report.json).

| Tier | **Retrievable today** |
|------|------------------------|
| **A** (10 classes) | **100%** |
| **B** (package binaries) | **~60%** — SSH path implemented; needs operator SSH config on backup |
| **C** (8 classes) | **~50%** — computers, API roles, distribution points **POSSIBLE**; FileVault/API integrations **PARTIAL**; certificates N/A; LDAP/MDM **BLOCKED** (platform) |

**Endpoint registry:** **36/41** verified (**88%** of catalog rows).

### Operator: optional privilege follow-up

- **View Disk Encryption Recovery Key** — unlocks FileVault bulk CSV (currently PARTIAL)
- Re-run `probe.py` → `scripts/sync_registry_from_probe.py` after any Jamf role change

---

## Release & operations

| Item | **Complete** |
|------|--------------|
| CI (`pytest`, Swift tests, registry sync) | **100%** |
| Private `release.yml` | **100%** |
| Public publish | **100%** (automation); **v0.3.0** pending tag workflow |
| UAT → promote (prerelease first) | **~90%** — `publish_public_release.sh` uses `--prerelease` |
| Live export parity workflow | **~50%** — documented, no recorded run |
| Production smoke (full backup + lab restore dry-run) | **~45%** — harness ready, operator run pending |

---

## Permanent platform limits (not gaps)

LDAP bind passwords (masked), MDM trust (re-enrollment), certificates endpoint absent on 11.23.1. Documented in `gaps/` — do not code around.

---

## Honest scope line (operator-facing)

**On-prem + MySQL restore** is the closest path to full fidelity. **Cloud** export is configuration + inventory + binaries + vaulted secrets — **not** a literal clone. Existing devices require **re-enrollment** on a new server.

---

## One-line status

Jamf Dossier **v0.4.0** closes dev-addressable export gaps: registry filter (expected-unavailable skips), plain-text script fallback, DR manifest on every run, and operator smoke/doc verification. **~12%** remains operator-only (FileVault privilege, lab live restore sign-off).
