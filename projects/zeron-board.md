# ZeronBoard (Material You Keyboard)

## Project Info
- **Base**: HeliBoard (fork of AOSP/OpenBoard keyboard)
- **New Name**: ZeronBoard
- **Package**: com.zeron.keyboard
- **Version**: 1.0 (versionCode 100)
- **Local Path**: /storage/emulated/0/zeron-keyboard/
- **GitHub Repo**: https://github.com/ZeronModz/zeron-board
- **License**: GPL-3.0

## What Was Done
1. Cloned HeliBoard → rebranded to ZeronBoard
2. Added 4 Material You color themes:
   - **Material Green** (DEFAULT) - from Theme1
   - **Material Red** - from Theme2
   - **Material Blue** - from Theme3
   - **Material Yellow** - from Theme4
3. Keyboard UI redesigned with Gboard-like 8dp rounded corners
4. Default style: Rounded + Material Green theme
5. Material Light activity theme
6. GitHub Actions CI: auto-build on push to main

## Key Files Modified
- `app/build.gradle.kts` - app name, package, version
- `KeyboardTheme.kt` - 4 new theme constants + color implementations
- `Defaults.kt` - default theme = Rounded + Material Green
- `colors.xml` - Material You green accent colors
- `btn_keyboard_key_*.xml` - 8dp rounded corners
- `keyboard_popup_panel_background_rounded_base.xml` - 16dp radius
- `platform-theme.xml` - Material Light theme
- `strings.xml` - ZeronBoard branding

## Build Info
- CI: GitHub Actions (push to main triggers build)
- APK: ~24.9 MB debug APK
- Build time: ~5 minutes

## Theme Colors (Material Green Default)
- Primary: #4C662B (light) / #B1D18A (dark)
- Background: #F9FAEF (light) / #1A1C16 (dark)
- Key BG: #FFFFFF (light) / #2A2C26 (dark)
- Functional Key: #CDEDA3 (light) / #373C32 (dark)
- Text: #1A1C16 (light) / #F1F2E6 (dark)

## Date
- Created: 2026-09-20
