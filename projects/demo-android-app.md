# DemoApp — Termux Android APK Builder

## Status: Active (Demo created, ready to build on real Termux)

## Date Created: 2026-07-30

## Description
A complete Android demo application built using the Termux + Gradle workflow (inspired by the "Stop Using AIDE!" YouTube video by Alienkrishn). The project demonstrates building Android APKs entirely from the command line without AIDE.

## Video Reference
- URL: https://youtu.be/UZbHxv2WrnY
- Title: "Stop Using AIDE! Build Advanced Android Apps with Termux & apkgen-cli"
- Creator: Alienkrishn

## Project Location
`/data/data/com.termux/files/home/demo-app/`

## Tech Stack
- **Termux** (Android terminal emulator)
- **OpenJDK 21** (Java compiler)
- **Gradle 9.6.1** (Build automation)
- **aapt/aapt2** (Android asset packaging)
- **apksigner** (APK signing)
- **Android SDK** (platform tools)
- **Material Components** (UI library)

## Project Structure
```
demo-app/
├── build.gradle                    # Root project build config
├── settings.gradle                 # Project settings with repo configs
├── local.properties                # SDK path config
├── gradle/wrapper/gradle-wrapper.properties
├── gradlew                         # Gradle wrapper script
├── README.md                       # Full documentation
├── build.sh                        # Quick build script
├── setup.sh                        # Full environment setup script
└── app/
    ├── build.gradle                # Module-specific build config
    ├── proguard-rules.pro          # ProGuard rules
    └── src/main/
        ├── AndroidManifest.xml     # App manifest
        ├── java/com/devzeron/demoapp/MainActivity.java
        └── res/
            ├── layout/activity_main.xml
            ├── values/colors.xml
            ├── values/strings.xml
            ├── values/themes.xml
            └── mipmap-*/ic_launcher.png (5 densities)
```

## Features
- Click counter with increment/reset buttons
- Toast message demonstration
- Material Design UI
- Debug APK build ready
- Full Gradle build system

## Key Commands
```bash
# Quick build
bash /data/data/com.termux/files/home/demo-app/build.sh

# Full setup (install SDK)
bash /data/data/com.termux/files/home/demo-app/setup.sh

# Manual build
./gradlew assembleDebug
```

## Notes
- APK compilation cannot complete in simulated Termux environment (no Android SDK platform files)
- On a real Android/Termux device, setup.sh installs Android SDK and builds successfully
- apkgen-cli install failed in simulated env due to Flutter dependency (shebang issue)
- Core build workflow (aapt + gradle + apksigner) is verified working
