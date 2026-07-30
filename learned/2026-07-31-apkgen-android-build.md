# Using apkgen-cli on Termux (Android)

## Key Learnings
- `apkgen-cli` (v1.4.0) creates Android projects with Java/Kotlin/Flutter/Native C++
- Build system: Gradle with AGP 8.13.0
- SDK location: `/data/data/com.termux/files/usr/opt/android-sdk/`
- Java: OpenJDK 21.0.12
- Gradle: Available at `/data/data/com.termux/files/usr/bin/gradle`

## Build Commands
```bash
apkgen create <dir>         # Create Java project
apkgen build debug          # Build debug APK
apkgen build release        # Build release APK (unsigned)
# When TTY not available, use gradle directly:
./gradlew assembleDebug --no-daemon
```

## Material 3 on Android
- Parent theme: `Theme.Material3.DayNight.NoActionBar`
- Material library: `com.google.android.material:material:1.13.0`
- Color roles: colorPrimary, colorOnPrimary, colorPrimaryContainer, etc.
- Do NOT use `md3_` prefixed attrs — they don't exist in public API
- Do NOT use `colorSurfaceTint` — not a standard theme attr

## Issues Encountered
- `apkgen` can't handle interactive TTY in Termux — use gradle directly
- AAPT2 daemon crashes with `aapt2FromMavenOverride` — use system gradle
- Build needs `--no-daemon` flag to avoid daemon issues in Termux
