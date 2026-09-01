# MSAPatcher Legacy Migration Design

## Goal
Migrate the useful MSAPatcher 7.4 script catalog into the Android Studio DirectDex application while replacing the old script-driven controller with a copy-first, decode/scan/transform/rebuild/sign/verify pipeline.

## Architecture
The Android/Kotlin pipeline owns project state and output. Legacy scripts and binaries are retained under `assets/legacy/` for compatibility and metadata. `LegacyModuleRegistry` reads the old `.prop` catalog and exposes modules to the UI. `LegacyWorkerRunner` may execute only modules classified as compatible utility/analyzer workers; modules whose purpose is signature/integrity/licensing/protection bypass are retained as optional legacy assets but are not wired into an execution path.

## Primary flow
`Import APK/APKS → Copy source → Extract working copy → Decode classes*.dex → Scan → Apply authorized transformer → Recheck → Rebuild DEX → Repack APK → Sign → Verify → Output`.

## Original-file policy
The selected source is copied before any processing. SHA-256 is stored and checked again before final output. No stage writes to the user's selected source URI.

## Output policy
Only a verified, signed rebuilt APK is presented as final output. Intermediate unsigned APKs stay inside the private project workspace.

## Legacy compatibility
All legacy `.sh`, `.prop`, supporting worker metadata and `assets/bin` payloads from the last MSAPatcher build are retained in the new source tree. They are not the controller. Project-folder scripts receive the generated private working directory if they are compatible with the new pipeline.

## Security boundary
Signing of rebuilt output is active. Signature/integrity/licensing/protection detection is active. Legacy modules intended to bypass those controls may be retained as optional package assets for compatibility, but this build does not connect them to an executable action.
