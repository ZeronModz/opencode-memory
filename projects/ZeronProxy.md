# ZeronProxy - Project State

## Overview
- **Path**: `/storage/emulated/0/Download/ZeronProxy/`
- **Type**: Android Kotlin + C++ NDK app + Vercel API + Next.js Admin panel
- **Package**: `dev.zeron.proxy`
- **GitHub**: `ZeronModz/zeron-proxy` (private)
- **CI/CD**: GitHub Actions (auto-build on push to main)

## Architecture
| Component | Tech | Status |
|-----------|------|--------|
| Android App | Kotlin + Material 3 + C++ NDK | ✅ Code complete |
| Floating Panel | FloatingLoginService + FloatingPanelService | ✅ |
| Security | AES-256-GCM, SHA-256, HMAC, anti-tamper | ✅ Kotlin fallback |
| API | Vercel Serverless (Node.js) | ✅ Code complete |
| Admin Panel | Next.js + Tailwind CSS | ✅ Code complete |
| CI/CD | GitHub Actions | ✅ Configured |

## Build Status
- **Local build**: ❌ IMPOSSIBLE on ARM phone (Android SDK tools are x86_64)
- **CI build**: ✅ GitHub Actions ubuntu-latest (x86_64) will build APK
- Native C++ build: Optional, enabled via `-PnativeBuild=true`

## Key Files
- `app/build.gradle` — conditional native build with `buildNative` property
- `app/src/main/java/dev/zeron/proxy/security/NativeBridge.kt` — Kotlin fallback when native lib missing
- `.github/workflows/ci.yml` — CI/CD with `-PnativeBuild=true`
- `gradle/wrapper/gradle-wrapper.properties` — Gradle 8.4

## Assets (from original proxy.apk)
- `AA.zip` (72KB) — main bypass payload
- `Bypass.zip` (5KB) — default/reset bypass
- `fonts/zx_mumun.ttf`, `fonts/zxnn.ttf` — original fonts
- `fonts/zeron.ttf`, `fonts/zeronx.ttf` — additional fonts

## Credentials
- App login password: `NIROB_BBZ`
- Admin: username `admin`, password `ZeronProxyAdmin2024!`
- Debug keystore: `app/keystore.jks` (storePass: android)

## Next Steps
1. User needs to set up GitHub repo secrets for CI/CD (VERCEL_TOKEN, etc.)
2. Deploy Vercel API: `cd api && vercel --prod`
3. Deploy Admin panel: `cd admin && npm run build && vercel --prod`
4. Update `SecurityConfig.API_BASE_URL` with real Vercel API URL
5. Download APK from GitHub Actions Artifacts

## Last Updated
- 2026-09-13: Initial commit pushed, CI/CD configured
- 2026-09-13: Fixed missing drawable resources (ic_logo, ic_key, ic_telegram, ic_close, etc.) and removed all font/inter_* references from layouts
