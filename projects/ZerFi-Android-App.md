# ZerFi Android App (Updated)

## Project Info
- **GitHub**: https://github.com/ZeronModz/ZerFi
- **Type**: Android APK (Material Design 3) — Standalone WPS Tool
- **Built**: 2026-07-31 (v2)
- **Package**: com.zerfi.app
- **APK**: /data/data/com.termux/files/home/ZerFi.apk (15MB)

## Architecture (v2 - Fixed + Standalone)
- **No Termux dependency** — direct root shell commands to system binaries
- **No Python dependency** — uses `wpa_cli`, `iw`, `ip` directly
- **Material Design 3** with proper color selectors

## Key Fixes from v1
1. **Crash fix**: BottomNav tint uses ColorStateList selector instead of direct color
2. **Crash fix**: NavHostFragment uses proper `app:navGraph` + `app:defaultNavHost`
3. **Crash fix**: All fragments use `isAdded()` checks before UI operations
4. **Crash fix**: ViewBinding removed, safer `findViewById` after `onViewCreated`
5. **Script → Native**: Removed Python script execution, uses `wpa_cli` etc directly

## Structure
```
ZerFiApp/
├── app/src/main/
│   ├── AndroidManifest.xml  (root, wifi, location, internet)
│   ├── java/com/zerfi/app/
│   │   ├── MainActivity.java
│   │   ├── ui/fragments/
│   │   │   ├── HomeFragment.java     (Root check + quick nav)
│   │   │   ├── ScanFragment.java     (iw scan via root)
│   │   │   ├── AttackFragment.java   (wpa_cli Pixie/Brute/PBC)
│   │   │   └── SessionsFragment.java
│   │   └── utils/
│   │       ├── RootShell.java        (id via su)
│   │       └── ShellExecutor.java    (Async sh -c + output)
│   └── res/ (Material 3, light/dark, navigation graph)
├── build.gradle, settings.gradle
└── gradle/
```

## What App Does
- **Root check**: `su -c id` → checks uid=0
- **WiFi Scan**: `iw dev wlan0 scan` via root
- **Pixie Dust**: `wpa_cli -i wlan0 wps_pin BSSID PIN`
- **Bruteforce**: `wpa_cli` PIN sequence
- **PBC**: `wpa_cli -i wlan0 wps_pbc`

## Requirements (Device)
- Rooted Android (Magisk/KernelSU)
- WiFi chipset with WPS support
- Built-in wpa_supplicant (present in all Android)
- No Termux needed
