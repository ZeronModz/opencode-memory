# Problem: Cannot Build Android APK on ARM Termux

## Date: 2026-09-13

## Problem
Android SDK build tools (AAPT2, NDK compiler, etc.) are all x86_64 binaries. They cannot run on ARM-based Android phones (Termux). This makes building Android APKs locally impossible on ARM devices.

## Root Cause
- AAPT2: `ELF 64-bit LSB pie executable, x86-64` — not executable on ARM
- NDK clang++: `ELF 64-bit LSB executable, x86-64` — not executable on ARM
- No x86_64 emulation available (no box64/box86)
- proot-distro Ubuntu still runs as ARM (aarch64)

## Solution Applied
1. Made native C++ build optional via Gradle property `nativeBuild`
2. `app/build.gradle`: `def buildNative = project.hasProperty('nativeBuild') ? project.property('nativeBuild').toBoolean() : false`
3. NativeBridge.kt: Added Kotlin fallback implementations when native lib unavailable
4. CI workflow: Pass `-PnativeBuild=true` on x86_64 Ubuntu runners
5. GitHub Actions CI builds the APK automatically on push

## Files Changed
- `app/build.gradle` — conditional native build
- `app/src/main/java/dev/zeron/proxy/security/NativeBridge.kt` — fallback implementations
- `app/src/main/java/dev/zeron/proxy/ZeronProxyApp.kt` — guarded native calls
- `app/src/main/java/dev/zeron/proxy/ui/SplashActivity.kt` — guarded native calls
- `.github/workflows/ci.yml` — `-PnativeBuild=true` flag

## Prevention
- Always build Android APKs on x86_64 machines or CI/CD
- On ARM devices, use CI for APK generation
- Keep native code optional for portability
