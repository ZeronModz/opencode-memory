# Termux Tools Reference — APK Building

## Installed Packages
| Package | Version | Role | Install Command |
|---------|---------|------|-----------------|
| openjdk-21 | 21.0.12 | Java compiler | `pkg install openjdk-21` |
| gradle | 9.6.1 | Build automation | `pkg install gradle` |
| aapt | 16.0.0_r4 | Asset packaging (v1) | `pkg install aapt` |
| aapt2 | 16.0.0_r4 | Asset packaging (v2) | `pkg install aapt2` |
| apksigner | 37.0.0 | APK signing | `pkg install apksigner` |
| android-sdk | 36.0.0 | SDK meta-package | `pkg install android-sdk` |
| zip | 3.0-7 | ZIP utility | `pkg install zip` |
| unzip | 6.0-10 | Unzip utility | `pkg install unzip` |

## Setup Workflow (from video: "Stop Using AIDE!")
```bash
1. Install Termux from F-Droid (not Play Store)
2. Install Termux:API add-on
3. pkg update && pkg upgrade
4. pkg install openjdk-21 gradle aapt aapt2 apksigner zip unzip
5. Configure Android SDK (sdkmanager)
6. Use apkgen-cli OR Gradle directly to build APKs
```

## Key Paths
- Termux home: /data/data/com.termux/files/home/
- Android SDK (default): $HOME/android-sdk
- Gradle cache: $HOME/.gradle/caches/
- AAPT2 ARM64 binary: /data/data/com.termux/files/usr/bin/aapt2

## Common Fixes
- sdkmanager shebang: `#!/usr/bin/env` → `#!/bin/sh`
- aapt2 x86_64 in Gradle cache: replace with Termux ARM64 aapt2
- local.properties: `sdk.dir=/data/data/com.termux/files/home/android-sdk`
