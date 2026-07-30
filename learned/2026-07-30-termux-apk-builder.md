# Learned: Termux Android APK Building

## Date: 2026-07-30

## Key Findings

### apkgen-cli
- CLI tool for Termux to generate Android APKs from templates
- Supports Java, Kotlin, Flutter, and Native C++ projects
- Installed via TermuxVoid repository: `apt install apkgen-cli`
- GitHub: https://github.com/termuxvoid/apkgen-cli
- In simulated Termux env, install fails due to Flutter shebang issue (`/usr/bin/env` not found)

### Android Build Tools in Termux (termuxvoid repo)
All available via `apt`:
- `openjdk-21` — Java compiler (already working)
- `gradle` — Build automation (already working)
- `aapt` / `aapt2` — Android asset packaging (already working)
- `apksigner` — APK signing (already working)
- `android-sdk` — SDK meta-package (installed but minimal)
- `zip` / `unzip` — Archive handling

### Building APKs Without Full SDK
- aapt, javac, jarsigner, zip/jar can create a minimal signed APK manually
- Full build requires Android platform files (android.jar, build-tools) from sdkmanager
- Gradle-based workflow handles dependency management and packaging automatically

### Key Workflows
1. **apkgen-cli**: `apkgen create myapp && cd myapp && apkgen build debug`
2. **Manual Gradle**: Create project → `./gradlew assembleDebug` → APK in `app/build/outputs/apk/debug/`
3. **Full setup**: Run `setup.sh` to install Android SDK, then build

### Termux Environment Notes
- Running on ARM64 (aarch64) architecture
- TermuxVoid repository configured (has apkgen-cli, android-sdk)
- `/bin/env` exists, `/usr/bin/env` does not (causes shebang issues)
- Google Play Store Termux has limitations vs F-Droid version
