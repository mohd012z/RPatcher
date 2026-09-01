# Direct DEX Rebuild Engine Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an Android-native APK workspace pipeline that imports an APK, copies it without touching the original, extracts it, decodes every classes*.dex, scans/optionally transforms authorized code, rebuilds DEX files, repacks the APK while preserving resources, verifies structure, then hands off to a separate signing stage.

**Architecture:** Avoid full resource recompilation for the first reliable engine. AndroidManifest.xml, resources.arsc, assets and native libraries are copied from the original ZIP byte stream; only classes*.dex entries are replaced. Google-maintained smali/baksmali/dexlib2 perform DEX decode/rebuild.

**Tech Stack:** Android/Kotlin, AGP 8.13.2, Gradle 8.13, Google smali 3.0.7.

**Spec:** This plan implements the approved flow: Import → Copy → Extract → Scan → Modify → Recheck/Rebuild → Compile/Repack → Output, with the source APK immutable.

## Global Constraints
- Input source is immutable; processing uses a copied APK.
- Only APK input in V1; APKS split-aware output is a separate task.
- Default transformation is NO-OP to validate decode/rebuild safely.
- Remove old META-INF signatures when repacking; never pretend an altered APK retains the original signer.
- Output is not considered installable until signing verification is implemented.

---

### Task 1: Workspace and immutable source
**Files:** `Workspace.kt`, `WorkspaceManager.kt`
- [x] Create per-run source/work/state/output directories.
- [x] Copy URI to source/original.apk.
- [x] Store and re-check SHA-256.

### Task 2: Safe APK extraction and multidex inventory
**Files:** `ZipWorkspaceExtractor.kt`, `DexInventory.kt`
- [x] Reject ZIP path traversal.
- [x] Map classes.dex/classesN.dex to smali/smali_classesN.
- [x] Add unit tests.

### Task 3: DEX decoder/rebuilder
**Files:** `DexDecoder.kt`, `DexRebuilder.kt`
- [x] Decode every DEX with baksmali.
- [x] Rebuild every smali folder with smali.
- [ ] Compile in Android Studio against Google Maven and adjust any API signature drift discovered by the compiler.

### Task 4: Scanner and modification boundary
**Files:** `Scanner.kt`, `Transformer.kt`
- [x] Add generic scan summary.
- [x] Keep default transformer NO-OP.
- [ ] Add user-owned transformation plugins only after tests for each transformation.

### Task 5: Repack without AAPT
**Files:** `ApkRepacker.kt`, `ApkVerifier.kt`
- [x] Stream-copy original ZIP entries.
- [x] Replace only rebuilt classes*.dex.
- [x] Strip old META-INF signature entries.
- [x] Verify manifest presence and DEX count.

### Task 6: Signing and install verification
**Files:** to be added after V1 compile gate
- [ ] Add an explicit developer-key signer using a maintained APK signing library.
- [ ] Verify resulting signing schemes before moving to final output.
- [ ] Never reuse or imitate the original app signature without its real private key.
