# APK Analysis: LIGHT IS BACK PORXCY 1.0

## File
- Original: `/storage/emulated/0/MT2/mcp/LIGHT IS BACK PORXCY_1.0.apk` (6.4 MB)
- Filename obfuscated with Unicode Mathematical Bold characters (codepoints 0x1d5d4-0x1d5eb) — visible letters only
- Copy: `/data/data/com.termux/files/usr/tmp/opencode/app.apk`

## Identity
- Package: `com.mcpanel`
- Label: LIGHT IS BACK PORXCY
- versionName 1.0 / versionCode 1
- minSdk 21, targetSdk 28, compileSdk 33
- Built with **Sketchware** (com.AndroidSketchwareMaster.*, SketchwareUtil)
- Launcher: com.mcpanel.MainActivity
- Multi-dex (5 dex files)

## Permissions (dangerous)
- MANAGE_EXTERNAL_STORAGE / WRITE_MEDIA_STORAGE / all-file access
- SYSTEM_ALERT_WINDOW (floating overlay)
- FOREGROUND_SERVICE
- INTERNET
- RETRIEVE_WINDOW_CONTENT / INTERNAL_SYSTEM_WINDOW / HIDE_NON_SYSTEM_OVERLAY_WINDOWS
- **moe.shizuku.manager.permission.API_V23** (Shizuku privileged shell)
- BIND_EXTERNAL_STORAGE_SERVICE

## What it does
1. **Login gate** — hardcoded password `NIROB_BBZ` in MainActivity.button1
2. **Shizuku integration** — ShizukuProvider registered; ShizukuMaster requests permission (code 69).
   - ShizukuShell runs `sh -c <cmd>` via `Shizuku.newProcess` — privileged shell without root.
3. **Floating overlay menu** (type 2038) — draggable "mod panel" window added via WindowManager.
4. **Cheat injection** — switches call `_ZipUnzip_Aseat_name(zip, dest)`:
   - Unzips `AA.zip` or `Bypass.zip` (from assets) then `unzip -o` into:
     `/storage/emulated/0/Android/data/com.dts.freefiremax/files/`
   - **com.dts.freefiremax = Garena Free Fire MAX**
5. **Files shipped in zips:**
   - AA.zip: Assembly-CSharp-patch.bytes, LocalConfig.json {"testCodePatch":true,"resetGuest":true},
     `_.kmteam-ffmax-state` {"modules":"aim-esp"}, ffrtc_log.txt, reportnew.db (SQLite)
   - Bypass.zip: Assembly-CSharp-patch.bytes (34KB), ShaderStripSettings, localConfig.json, vqcfg.bin
   - Assembly-CSharp-patch.bytes header: `IFix.ILFixInterfaceBridge, Assembly-CSharp...UnityEng...` = **IFix runtime IL patch** for Unity IL2CPP game
   - `_kmteam-state` module **aim-esp** = aimbot + ESP (wallhack) cheat
6. **Time-bomb / expiry**:
   - SharedPreferences "time_check" anti-rollback: if device time < last_time → "Date & Time Incorrect" dialog
   - Hard expiry: Calendar 2026-09-14 23:59:59 → "Panel Has been Expired" → update button
7. **Telegram promo** — all buttons link to `https://t.me/SPEED_X_OFFICIAL1`
8. ApkIntegrityChecker present but **non-functional** (placeholder strings "YourOriginalApkSHA256Here")

## Verdict
- Not a spyware/stealer (only external URL is the Telegram channel).
- It IS a **game-cheating tool**: uses Shizuku to write patched Unity assembly/Lua-config into Free Fire MAX data dir to enable aimbot/ESP and bypass anti-cheat.
- Risk: granting Shizuku = effectively near-root shell to a random Telegram-distributed mod panel. High risk if misused; may get game account banned.

## Technique notes
- Shizuku lets apps run privileged shell ops without root (Android 11+ wireless ADB or root activation).
- IFix patches Unity Assembly-CSharp at runtime → bypasses signature/integrity checks in modded games.
- targetSdk 28 = bypasses scoped-storage restrictions (old target still allowed pre-Android 11 behavior).
- Unicode math-bold filename = casual obfuscation to avoid keyword detection.
