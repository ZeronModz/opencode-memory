# ZerFi Android App (v3 — Fully Standalone)

## Project Info
- **APK**: /data/data/com.termux/files/home/ZerFi.apk (16MB)
- **Package**: com.zerfi.app
- **Min SDK**: 29 (Android 10)

## What it does (same as Python script)
1. **Opens app** → auto-requests root permission (su)
2. **Root granted** → extracts bundled binaries (pixiewps, wpa_supplicant, wpa_cli)
3. **Starts wpa_supplicant** → creates own instance with config
4. **Scans** for WPS-enabled networks
5. **Auto-attacks** vulnerable networks with Pixie Dust
6. **Shows results** in real-time console

## Bundled binaries (in assets/)
| Binary | Size | Arch |
|--------|------|------|
| pixiewps | 91KB | aarch64 |
| wpa_cli | 125KB | aarch64 |
| wpa_supplicant | 2.3MB | aarch64 |

## Architecture
```
app/src/main/assets/
├── bin/pixiewps
├── bin/wpa_supplicant
├── bin/wpa_cli
└── engine.sh          (main attack script)

app/src/main/java/com/zerfi/app/
├── MainActivity.java
├── ui/fragments/
│   ├── HomeFragment.java    (auto-run engine)
│   ├── ScanFragment.java
│   ├── AttackFragment.java
│   └── SessionsFragment.java
└── utils/
    ├── RootShell.java
    ├── ShellExecutor.java
    └── ZerFiEngine.java     (extract + execute engine.sh)
```

## Key Files
- **engine.sh**: Shell script that replicates the Python ZerFi logic
  - Starts wpa_supplicant
  - Scans for WPS networks
  - Runs Pixie Dust attacks via wpa_cli + pixiewps
  - Saves results
- **ZerFiEngine.java**: Android engine that extracts assets and runs engine.sh
- **HomeFragment**: Shows real-time output in Material 3 console

## Requirements
- Rooted Android (Magisk/KernelSU)
- ARM64 device
- Working WiFi chipset
- No Termux needed
