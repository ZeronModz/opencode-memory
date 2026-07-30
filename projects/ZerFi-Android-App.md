# ZerFi Android App

## Project Info
- **GitHub**: https://github.com/ZeronModz/ZerFi
- **Type**: Android APK (Material Design 3) wrapping ZerFi WPS tool
- **Built**: 2026-07-31
- **Package**: com.zerfi.app
- **APK Location**: /data/data/com.termux/files/home/ZerFi.apk (15MB)

## Project Structure
```
ZerFiApp/
├── app/
│   ├── build.gradle          (namespace: com.zerfi.app, minSdk 29, targetSdk 36)
│   ├── src/main/
│   │   ├── AndroidManifest.xml  (root, wifi, internet, location permissions)
│   │   ├── java/com/zerfi/app/
│   │   │   ├── MainActivity.java       (Bottom nav + NavHost)
│   │   │   ├── ui/fragments/
│   │   │   │   ├── HomeFragment.java    (Dashboard with root check + quick actions)
│   │   │   │   ├── ScanFragment.java    (WiFi scanner with shell output)
│   │   │   │   ├── AttackFragment.java  (Pixie/Brute/PBC attack runner)
│   │   │   │   └── SessionsFragment.java (Saved sessions viewer)
│   │   │   └── utils/
│   │   │       ├── RootShell.java       (Root detection utility)
│   │   │       └── ShellExecutor.java   (Async root shell command executor)
│   │   └── res/
│   │       ├── layout/
│   │       │   ├── activity_main.xml     (CoordinatorLayout + BottomNavigationView)
│   │       │   ├── fragment_home.xml     (Material 3 cards dashboard)
│   │       │   ├── fragment_scan.xml     (Interface input + output console)
│   │       │   ├── fragment_attack.xml   (Attack mode radio + config + output)
│   │       │   └── fragment_sessions.xml (RecyclerView for sessions)
│   │       ├── values/themes.xml        (Material 3 Day theme - Teal seed)
│   │       ├── values-night/themes.xml  (Material 3 Night theme)
│   │       ├── values/colors.xml        (Light palette)
│   │       ├── values-night/colors.xml  (Dark palette)
│   │       ├── navigation/nav_graph.xml (4 destinations)
│   │       └── drawable/               (Vector icons: home, scan, attack, sessions)
│   └── proguard-rules.pro
├── build.gradle
├── settings.gradle
└── gradle/
```

## Architecture
- **Navigation**: Android Navigation Component with BottomNavigationView
- **UI**: Material Design 3 (Theme.Material3.DayNight.NoActionBar)
- **Colors**: Teal/Cyan seed (#009688) — Material 3 tonal palette
- **Shell**: su-based root execution via ProcessBuilder
- **Theming**: Dynamic light/dark with full Material 3 color roles

## Features
- Root status check on Home
- WiFi scan via `iw` command
- Pixie Dust / Bruteforce / PBC attack modes
- Real-time terminal output in Material 3 cards
- Stop running attacks

## Build Info
- SDK: 36 (compile), 29 (min), 36 (target)
- AGP: 8.13.0
- Material: 1.13.0
- Build command: `cd ZerFiApp && ./gradlew assembleDebug --no-daemon`
- APK: app/build/outputs/apk/debug/app-debug.apk (15MB)
