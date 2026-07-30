# DemoApp — Termux Android APK Builder

## Status: COMPLETE — APK Built Successfully

## Date: 2026-07-31 02:26

## Description
Successfully built a complete Android APK using the Termux + apkgen-cli workflow from the YouTube video "Stop Using AIDE! Build Advanced Android Apps with Termux & apkgen-cli" by Alienkrishn.

## Video Reference
- URL: https://youtu.be/UZbHxv2WrnY
- Title: "Stop Using AIDE! Build Advanced Android Apps with Termux & apkgen-cli"
- Creator: Alienkrishn

## How it Works (apkgen-cli workflow)
```bash
# Step 1: Create project from template
apkgen create demo-app

# Step 2: Navigate to project
cd demo-app

# Step 3: Build debug APK
apkgen build debug

# Output: app/build/outputs/apk/debug/app-debug.apk
```

## Tools Used
| Tool | Version | Role |
|------|---------|------|
| apkgen-cli | 1.4.0 | Project scaffolding & build orchestration |
| OpenJDK 21 | 21.0.12 | Java compiler |
| Gradle 9.6.1 | 9.6.1 | Build automation |
| Android SDK | 36.0.0 | Platform & build tools (sdkmanager) |
| aapt / aapt2 | 16.0.0_r4 | Asset packaging |
| apksigner | 37.0.0 | APK signing |

## Built APK Details
- **File**: `demo-app/app/build/outputs/apk/debug/app-debug.apk`
- **Size**: 6.5MB
- **Package**: `com.devzeron.demoapp`
- **Version**: 1.0
- **compileSdk**: 36
- **minSdk**: 24
- **targetSdk**: 36
- **MainActivity**: `com.devzeron.demoapp.MainActivity`
- **Features**: Click counter + toast message, Material theme

## Key Insights
- apkgen-cli works on Termux but requires Android SDK platform files installed first
- The `sdkmanager` shebang needed fixing (`#!/bin/bash` → `#!/bin/sh`) due to missing `/usr/bin/env`
- Gradle cache x86_64 aapt2 must be replaced with ARM64 Termux aapt2 for builds to work on Android ARM64 devices
- The video's approach (apkgen-cli instead of AIDE) is a solid workflow for CLI-based Android development

## Project Location
`/data/data/com.termux/files/home/demo-app/`
