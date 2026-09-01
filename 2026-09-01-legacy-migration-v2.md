# Legacy Migration V2 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Merge the legacy MSAPatcher catalog into DirectDex and produce a signed-output pipeline controlled by Kotlin.

**Architecture:** The DirectDex engine remains the controller. Legacy scripts are packaged as assets and surfaced through a registry/runner. DEX decode/rebuild uses Google smali. Final APK signing uses Android Keystore + apksig.

**Tech Stack:** Android/Kotlin, Google smali 3.0.7, Android apksig, Android Keystore, JUnit 4.

**Spec:** `docs/superpowers/specs/2026-09-01-legacy-migration-design.md`

## Global Constraints
- Never modify the selected source file in place.
- Keep every legacy script/property in the package for compatibility.
- Kotlin pipeline is the only build controller.
- Do not claim output success until signing and verification succeed.

---

### Task 1: Legacy catalog packaging
- [ ] Add a regression test requiring the legacy catalog and `SignatureKiller` asset to be present.
- [ ] Copy legacy scripts/bin into `app/src/main/assets/legacy/`.
- [ ] Add registry and asset installer.
- [ ] Verify static regression test passes.

### Task 2: Pipeline signing/final verification
- [ ] Add failing source regression checks for SIGN and FINAL_VERIFY stages.
- [ ] Add Android Keystore signing identity.
- [ ] Add `ApkSignerEngine` and signature verification.
- [ ] Return signed APK from the pipeline.

### Task 3: Legacy migration UI
- [ ] Add category hub and legacy-module browser.
- [ ] Keep one primary rebuild action.
- [ ] Show progress and final output path.

### Task 4: Verification
- [ ] Run source regression suite.
- [ ] Run ZIP/source integrity checks.
- [ ] Attempt Gradle build; record exact environment blocker if unavailable.
