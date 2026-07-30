# apkgen-cli Full Documentation

## Source: https://github.com/termuxvoid/apkgen-cli

## Overview
apkgen is a CLI tool for Termux to generate Android APKs from templates: clone, rename package, restructure source, build debug/release.

## Prerequisites
- Termux installed from F-Droid (recommended) or GitHub
- TermuxVoid repository configured
- Android 7+ with ~1GB free storage
- Working internet connection for initial setup

## Installation
```bash
pkg update
pkg install openjdk-21 gradle aapt aapt2 apksigner zip unzip
apt install apkgen-cli -y
```

Verify: `apkgen help`

## Quick Start
```bash
# Scaffold a new Java project
apkgen create myapp

# Enter the project directory
cd myapp

# Build a debug APK
apkgen build debug

# Output: app/build/outputs/myapp-debug.apk
```

## Commands

### `create <directory> [--kotlin|--flutter|--nativecpp|--nativec]`
Clones the project template into a new directory and prompts for app name and package name. Defaults to Java if no flag is specified.

```bash
# Java project (default)
apkgen create myapp

# Kotlin project
apkgen create myapp --kotlin

# Flutter project
apkgen create myapp --flutter

# Native C++ project
apkgen create myapp --nativecpp

# Native C project
apkgen create myapp --nativec
```

### `build debug`
Compiles a debug APK with debug signing enabled (Android).
```bash
apkgen build debug
```

### `build release`
Compiles an unsigned release APK (Android). Sign manually with `apksigner` or `jarsigner`.
```bash
apkgen build release
```

### `build flutter`
Builds a debug APK for Flutter projects with split ABI outputs.
```bash
apkgen build flutter
```

### `clean`
Removes all build artifacts from the project directory.
```bash
apkgen clean
```

### `help`
Prints usage information.
```bash
apkgen help
```

## Command Reference Table
| Command | Description |
|---------|-------------|
| `create <dir>` | Scaffold Java project (default) |
| `create <dir> --kotlin` | Scaffold Kotlin project |
| `create <dir> --flutter` | Scaffold Flutter project |
| `create <dir> --nativecpp` | Scaffold Native C++ project |
| `create <dir> --nativec` | Scaffold Native C project |
| `build debug` | Build debug-signed APK (Android) |
| `build release` | Build unsigned release APK (Android) |
| `build flutter` | Build debug APK (Flutter, split ABI) |
| `clean` | Remove build outputs |
| `help` | Show help text |

## Output Structure
```
app/build/outputs/
├── myapp-debug.apk
└── myapp-release-unsigned.apk
```

## Project Template Structure (Java)
```
myapp/
├── build.gradle
├── settings.gradle
├── gradle.properties
├── local.properties
├── gradlew
├── gradlew.bat
├── gradle/wrapper/gradle-wrapper.jar
├── gradle/wrapper/gradle-wrapper.properties
└── app/
    ├── build.gradle
    ├── proguard-rules.pro
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/example/myapp/MainActivity.java
        ├── res/
        │   ├── layout/activity_main.xml
        │   ├── values/colors.xml
        │   ├── values/strings.xml
        │   ├── values/themes.xml
        │   ├── drawable-*/ic_launcher.png
        │   └── mipmap-*/ic_launcher.png
        └── res/xml/backup_rules.xml
```

## Templates Available
- **java** — Kotlin DSL Gradle, Material Design, androidx
- **kotlin** — Kotlin source + Kotlin DSL
- **flutter** — Flutter project with split ABI
- **nativec** — Native C project
- **nativecpp** — Native C++ project

## Key Files in Templates
- `gradle/libs.versions.toml` — Version catalog
- `app/build.gradle` — Module-level build config
- `app/src/main/AndroidManifest.xml` — App manifest
- `app/src/main/res/layout/activity_main.xml` — Default layout

## Build Process
1. `apkgen create` → clones template, prompts for config
2. `apkgen build debug` → runs gradle assembleDebug
3. APK is placed in `app/build/outputs/apk/debug/`

## Support
- TermuxVoid community (Telegram)
- GitHub issues
- MIT license

## Created By
Alienkrishn (https://github.com/Anon4You) — for security researchers, Termux-optimized builds
