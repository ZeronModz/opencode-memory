# Project: dev.zeron.guest — Garena Guest Injector (Sketchware Pro 608)

## Summary
Garena guest account tool. Scans storage for `guest*.dat`, shows paths in Material 3 UI,
long-press = preview + copy, short press = copy path. 3 fields (UID / Password / Path)
inject kore `guest_account_info.com.garena.msdk.guest_uid` + `guest_password` replace,
old file → `.zeron` backup.
Platform: Sketchware Pro (daydream v7), project #608, package `dev.zeron.guest`,
files dir `.sketchware/data/608/files/java/`.

## Files (java/, all compile-clean vs android-34 + material 1.14)
- `GuestCore.java` — logic: hasStorageAccess (Environment.isExternalStorageManager / <R → READ perm),
  hasNotifPermission (POST_NOTIFICATIONS), requestStorageAccess (ACTION_MANAGE_APP_ALL_FILES_ACCESS_PERMISSION
  + <R runtime), scanStorage (recursive, depth<=9, name ends .dat OR contains guest),
  preview (read <=128KB, checks guest_account_info), inject (JSONObject replace uid/pass,
  validate JSON + keys, backup `.zeron`, write new), copyToClipboard, toast.
- `GuestUi.java` — full programmatic Material 3 UI:
  - `setup(Activity)` — force `Theme_ZeronGuest` + `DynamicColors.applyToActivityIfAvailable`
    via SharedPreferences `g_injector/m3` guard + `a.recreate()` (once), statusbar=header color,
    auto-request notif perm.
  - `build(Activity)` returns ScrollView root: gradient header (#312E81→#6D28D9, M3 chip),
    permission card (storage + notification rows w/ grant buttons + live status),
    scan card (FilledButton SCAN STORAGE → background thread → progress bar → result cards,
    count pill), result rows (icon ✓/?, GUEST/RAW tag, name+path, tap=copy path, long=preview dialog),
    inject card (3 outlined TextInputLayout: Guest UID / Guest Password / path,
    INJECT button tertiaryContainer, status TextView selectable, green/red on OK/FAIL).
  - Colors resolved by ATTR NAME via `getIdentifier(name,"attr",pkg)` → no `com.google.android.material.R`
    dependency (sketchware R merge safety). Dialog = MaterialAlertDialogBuilder + androidx AlertDialog.
- `files/resource/values/themes.xml` — `<style name="Theme.ZeronGuest" parent="Theme.Material3.DayNight.NoActionBar"/>`.

## Compile test
`javac -source 8 -target 8 -bootclasspath android-34.jar -classpath cp:material-v1.14.0-alpha06` → clean (exit 0).
Test harness used stub `dev.zeron.guest.R.Theme_ZeronGuest` + stub `androidx.lifecycle.LifecycleOwner`
(runtime build e real R + androidx theke ashe).
JSON inject logic JVM-verified with exact sample `{"guest_account_info":{"com.garena.msdk.guest_uid":...,"com.garena.msdk.guest_password":...}}` — keys replaced, extra keys preserved (json library org.json).

## Setup on Sketchware (user manual)
1. Project → App Permissions add: `MANAGE_EXTERNAL_STORAGE`, `POST_NOTIFICATIONS`
   (optional READ/WRITE_EXTERNAL_STORAGE for Android<11 devices).
2. MainActivity onCreate event e ekta More Block:
   `GuestUi.setup(this); setContentView(GuestUi.build(this));`
3. Build → signed APK → sign with `/storage/emulated/0/sketchware/keystore/release_key.jks`
   (alias devzeron, pass devzeron123, PKCS12) via termux `apksigner`.

## Test checklist
- First open → permission card NOT GRANTED → tap GRANT opens All Files Access settings → back → GRANTED.
- SCAN STORAGE → finds guest*.dat → tap copies path, long-press shows preview dialog + copy buttons.
- Inject: fill UID/Pass/Path → INJECT → status green OK + `.zeron` backup created, original updated.
- Invalid path / wrong JSON → red FAIL with reason (validates before write).