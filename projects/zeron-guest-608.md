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

## ✅ REDESIGN 2026-08-18 — View-File Driven UI (GuestUi.java DELETED)
Old approach (GuestUi.java programmatic UI + `setContentView(GuestUi.build(this))`) replaced with
native Sketchware **view-file driven** UI — builder generates the layout from the encrypted `view` file.
GuestUi.java removed; the fork's MainActivity inflates `@main.xml` from `view`, and `GuestCore.java`
now contains renderResults + showPreviewDialog (dynamically built MaterialCardView result rows).

### Files
- `files/java/GuestCore.java` — updated: scanStorage now matches EXACT `guest100067.dat` only
  (case-insensitive); added `renderResults(Activity, LinearLayout, List<String>)` (M3 result cards:
  ✓/? icon, GUEST/RAW pill tag, name+path, click=copy path, long-click=preview dialog) and
  `showPreviewDialog(Context, path)` (MaterialAlertDialogBuilder, monospace scrollable body, COPY
  CONTENT / COPY PATH / CLOSE). Compile-clean vs android-34 + material 1.14 (verified).
- `files/java/GuestUi.java` — DELETED.
- `files/resource/drawable/bg_*.xml` — 7 ripple/shape drawables (btn primary / primaryContainer /
  errorContainer / tertiaryContainer, hero chip, count pill, result card). NOTE: build failed once on
  `#33930000A` (invalid hex) — fixed; all colors now valid 8-digit hex or `@color/md_theme_*`.

### view file (encrypted, rebuilt via gen_608_view.py + Crypt)
`@main.xml` 42 components — Material 3, user palette (`md_theme_*`):
`root_container` (LinearLayout, surface bg, 16/16/28/18 padding) →
- card_hero (MaterialCardView, primary #4C662B, radius 28dp) → hero_box → GUEST INJECTOR (22sp onPrimary),
  subtitle, chip (bg_chip_hero).
- card_perm (surfaceContainerHigh, radius 24dp, outlineVariant stroke) → perm_box → "Storage Access"
  (16sp bold), perm_status (STATUS: NOT GRANTED, error color), desc, btn_grant (GRANT ALL FILES ACCESS,
  bg_btn_errorcontainer), notif_row → notif_title/desc + btn_notif (ALLOW, bg_btn_primarycontainer).
- card_scan → scan_head (title + scan_count pill bg_pill), scan_hint, btn_scan (SCAN STORAGE,
  bg_btn_primary), progress (CircularProgressIndicator type-0, visibility gone, 48dp indColor primary),
  result_head "FOUND FILES", results_container, scan_empty.
- card_inject → inject_title, til_uid+et_uid, til_pass+et_pass (password_toggle, inputType 129),
  til_path+et_path, inject_hint, btn_inject (INJECT, bg_btn_tertiarycontainer), txt_inject_status.
- `@main.xml_fab` → _fab hidden (visibility gone).
Component schema (id/convert/type/parent/parentType/index/inject) mirrors project 4 (p4) entries:
  MaterialCardView type 36 (parentType 36 for children), TextInputLayout type 38, TextInputEditText type 5,
  LinearLayout 0, Button 3, TextView 4, CircularProgressIndicator 0.
Encryption: `Crypt enc logic_new.txt → view` (same RSA/AES as fork). Round-trip verified.

### logic file (encrypted, via gen_608_logic.py)
Single section `@MainActivity.java_onCreate_initializeLogic` + one `addSourceDirectly` block
(exact same pattern as working 604 calculator). Injected Java in MainActivity initializeLogic():
  statusbar/navbar = 0xFFF9FAEF + LIGHT_STATUS_BAR, hide ActionBar, DynamicColors.applyToActivityIfAvailable,
  findViewById all widgets, storage permission status reflection (GRANTED → green/gone), auto
  requestNotifPermission, btn_grant→requestStorageAccess, btn_notif→requestNotifPermission,
  btn_scan→(perm check)→progress + bg thread scanStorage→post→renderResults + count + empty msg,
  btn_inject→validate UID/pass/path + file exists→bg thread inject→post status.
All R.id.* used verified present in view.

### Fork facts (learned, confirmed from debug.txt)
- Fork = Sketchware Pro **v7.0.0-daydream-android33-68519a4 (150)** = same as projects 4/603/604
  (project_config: min_sdk 24, viewbinding true, xml_command true). 607 uses a different fork.
- Build flow (debug.txt 2026-08-18): aapt2 compile generated res → compile `files/resource` (imported)
  → aapt2 link. `mv com.google.android.material` local lib material-v1.14.0-alpha06 in libs/local_libs.
- MainActivity.java generated from `logic` sections `@MainActivity.java_<method>`.
- `files/resource` = imported res dir (compiled); **no plaintext resource dir** — project `resource`
  is an encrypted blob (use Crypt for values too if editing colors).
- mysc/list/<id>/project = encrypted project metadata (name, pkg, colors).
- Drawables MUST use valid hex (build fails hard on bad literal like #33930000A).

### Build status
- Last auto build attempt (23:23) failed ONLY at aapt2 link on the bad drawable hex — that is fixed now.
- User should now Build (signed) → expect runtime verification of new M3 layout + wiring.

## ✅ FIX 2026-08-19 — ViewBinding blocker solved (custom MainActivity.java override)
Latest build failed: generated `MainActivity.java` referenced `MainBinding` (viewbinding enabled) but
`MainBinding` wasn't generated → "Failed to compile Java files". Root cause: this fork generates
MainActivity.java from the `logic` file template that assumes ViewBinding.

### The proven pattern (from same-fork projects 4 & 603)
- **`files/java/MainActivity.java`** (custom, package `dev.zeron.guest`) **OVERRIDES** the generated
  activity. It extends `AppCompatActivity`, does `setContentView(R.layout.main)` + `findViewById`
  (NO ViewBinding). p4/603 work exactly this way.
- **`logic` file = EMPTY** (16-byte encrypted, decrypts to 0 bytes) — p4/603 logic decrypts to 0 bytes.
  Copy `data/4/logic` → `data/608/logic` (verified md5 match).
- Kept `files/resource/layout/main.xml` override (imported res wins over generated via aapt2 overlay).

### Current 608 state (all set for build)
- `data/608/logic` = p4's empty logic (16 bytes, decrypts to 0).
- `files/java/MainActivity.java` (NEW, 6.9KB) — custom override: DynamicColors, statusbar/navbar
  0xFFF9FAEF, findViewById all 16 ids, refreshPermStatus (GRANTED/NOTIF status + btn_scan enable),
  auto requestNotifPermission on S+, btn_grant→requestStorageAccess, btn_notif→requestNotifPermission,
  btn_scan→bg thread scanStorage→post renderResults+count+empty, btn_inject→validate 3 fields→bg
  thread inject→post status.
- `files/java/GuestCore.java` unchanged (compile-clean).
- Verified: MainActivity + GuestCore **compile together cleanly** via javac vs android-34 + real
  appcompat 1.7.0 + core 1.13.1 + fragment 1.8.3 + material 1.14 + cardview (downloaded from
  dl.google.com maven; stubs only for androidx.annotation.NonNull, LifecycleOwner, ViewModelStoreOwner,
  HasDefaultViewModelProviderFactory + fake R — runtime build provides real ones).
- `data/608/view` untouched (still valid 42-comp main.xml + fab).
- Deps fetched locally for future checks: `/data/data/com.termux/files/usr/tmp/opencode/check/`
  (appcompat/core/fragment/drawer/customview/activity/savedstate/lifecycle/cardview jars).

### NEXT
- User: Build (signed) in Sketchware → expect: no MainBinding error, M3 layout inflates, scan/inject work.
- If build still regenerates MainActivity from empty logic → may need `enable_viewbinding:false` too.

## ✅ UI v2 2026-08-19 — scroll + auto-scan + notif alerts + premium M3 (from user feedback)
User feedback batch: (1) scroll hocche na (2) permission paile card_perm hide hobe (3) app open korle auto-scan + scan button (4) scan korar somoy notification alert (notif perm sob mongolo) (5) latest M3 widgets + premium design.
### Changes applied
- **layout/main.xml rebuilt** (all existing ids preserved):
  - Root = FrameLayout `root_container` → ScrollView `scroll` (fillViewport) → content LinearLayout (paddingBottom 112dp) → **ExtendedFloatingActionButton `fab_rescan`** (bottom|end, RESCAN + ic_refresh).
  - Hero = **FrameLayout `card_hero`** (background = gradient `bg_hero_gradient` #5B7A33→#394E1F, elevation 2dp) (MaterialCardView android:background override kore na → FrameLayout safe) + `hero_icon` (52dp bg_hero_icon #26FFFFFF, "GI") + title/sub + `hero_chip` (status text).
  - Perm card = **`card_perm` id added** (hidden when storage granted); LOCK icon square (bg_icon_soft); btn_grant = MaterialButton (bgTint errorContainer, corner 16, icon ic_lock); btn_notif = MaterialButton (icon ic_notifications).
  - Scan card = MaterialButton btn_scan (icon ic_search, bgTint primary), **`scan_progress`** = LinearProgressIndicator (indeterminate) + keep circular `progress`; tile length count pill; results_container/scan_empty kept.
  - Inject card = 3x **TextInputLayout FilledBox** (boxBackgroundColor surfaceContainerHighest, corner 16, startIcon ic_person/ic_lock/ic_folder, pass=password_toggle), btn_inject = MaterialButton (icon ic_send, bgTint tertiaryContainer).
  - New drawables (7 vector icons + 3 shapes): ic_search, ic_refresh, ic_lock, ic_notifications, ic_send, ic_person, ic_folder, bg_hero_gradient, bg_hero_icon, bg_icon_soft. (Standard M I paths.)
- **GuestCore.java**: added scan notifications — `SCAN_CHANNEL`/`SCAN_NOTIF_ID`(4001), `ensureScanChannel` (API26+, IMPORTANCE_DEFAULT, silent), `notifyScanStart` (ongoing, "Scanning storage...", smallIcon ic_search, CATEGORY_PROGRESS), `notifyScanDone(found)` ("N file(s) found"), `cancelScanNotif`. Guards hasNotifPermission + API<26 old Builder. Means notif permission ekhon kaj kore.
- **MainActivity.java**: added card_perm/hero_chip/fab_rescan/scan_progress wiring; `refreshPermStatus` → **card_perm GONE when storage granted** + hero_chip "AUTO-SCAN READY" vs "SETUP REQUIRED"; **auto-scan on open** (onResume, once, 250ms delay, flag autoScanned); fab_rescan click = rescan; doScan toggles scan_progress+progress and fires notifyScanStart → notifyScanDone (also cancel leaks). Did NOT keep background scan alive beyond onDestroy (no foreground service — ok for short scan).
### Verification
- javac (real androidx: appcompat/core/fragment/drawer/customview/activity/savedstate/lifecycle/cardview/coordinator + material + android-34) → **EXIT 0** both files (stubs only: NonNull, LifecycleOwner, ViewModelStoreOwner, HasDefaultViewModelProviderFactory, fake R).
- All ids in Java == ids in XML (diff empty). All drawables referenced exist. All 16 md_theme colors used defined. All XML well-formed (python parse).
- aapt2 host binary won't run on Android (exec format error) — trust fork build pipeline (worked before).
- **NOTES**: MaterialButton/LinearProgressIndicator/ExtendedFAB/FilledBox styles verified present in material-v1.14.0-alpha06 res. bg_btn_* old drawables unused but kept (harmless).

## ✅ UI v3 2026-08-19 — pinned hero, auto-fill+persist path, gap fix, credits card, M3 widgets everywhere
User feedback (Bangla scri pt): (1) sob scroll hobe **card_hero bade** (pinned) (2) scan pore et_path e path auto-fill (3) user path diye inject korle path **SharedPreferences** e save → reopen e thakbe (4) results_container e icon+name+path **gap** (sob icon er sathe lege jacche) (5) sob color **values** theke (theme only, tone-similar allowed) (6) **TextInputLayout model variety** (7) anek material widgets/animation use (8) **credit/About card** + apnar Telegram/GitHub (9) aro features ami.
### Changes
- **main.xml**: root = LinearLayout → **card_hero pinned** (top, na scroll) + FrameLayout(weight1) → ScrollView content (card_perm, card_scan, card_inject, card_about) + ExtendedFAB fab_rescan. Hero e 2 chips (hero_chip status + MATERIAL 3 tag).
- **Auto-fill**: scan done hole et_path e **first found path auto-fill**; result row tap karle o path fill + copy (renderResults e pathField + onFill callback param). et_path helperText "Tap a scan result to fill — saved between opens".
- **SharedPreferences** `guest_inject` (K_UID/K_PASS/K_PATH/K_AUTO): load on create, save via TextWatcher (sob 3 field), saveFields() on auto-fill/inject. Reopen → fields thake.
- **Gap fix**: renderResults row rebuilt — circular 42dp iconBox (ic_search on primaryContainer OVAL) + texts inset 12dp start + GUEST/RAW tag pill (valid? primaryContainer:errorContainer) + card bg surfaceContainerLow/stroke outlineVariant, row bottomMargin 10dp, **staggered fade+slide animation** (alpha 0→1, translationY, 55ms stagger, 240ms).
- **Theme colors everywhere**: all hardcoded rgb in renderResults + preview dialog → `R.color.md_theme_*`; main.xml hex→values tokens (`m3_hero_grad_start/center/end`, `m3_onPrimary_sub`, `m3_hero_icon_bg`). bg_hero_gradient/bg_hero_icon now use @color tokens.
- **M3 widgets more**: `MaterialSwitch` switch_auto (AUTO-SCAN ON OPEN, persisted K_AUTO), `Chip` Assist chip_scan_state (READY/SCANNING/N FOUND/NO FILES), TextInputLayout variety: uid=FilledBox, pass=**OutlinedBox**, path=FilledBox+clear_text+helperText, btn_paste=TextButton (clipboard→et_path), MaterialSwitch in scan card, MaterialButton TonalButton for Telegram/GitHub.
- **Credits card** (secondaryContainer bg): avatar "DZ" (bg_avatar oval), "Made by DevZeron", btn_telegram (t.me/ZeronModz) + btn_github (github.com/ZeronModz) open via ACTION_VIEW, version "v1.3 · Material 3".
- New drawables: ic_copy, ic_paste, ic_telegram, ic_github, bg_avatar. All colors from values only.
- Auto-scan on open ekhon switch-controlled (if off → manual only).
- **NOTES**: til_pass OutlinedBox startIconDrawable initially bhul kore @color diyechilam → @drawable/ic_lock fix. aapt2 host nai (Android), fork build pipeline e validate hobe. Compile EXIT 0 (full androidx classpath + material). ids/drawables/colors all matched, XML all well-formed (verified).
- Telegram/GitHub handle default rakha: ZeronModz (@ZeronModz / github.com/ZeronModz) — user chaile bolo, change korbo.

## ✅ UI v4 2026-08-19 — THEME COLOR ROOT FIX + icons + pretty JSON preview + hero margin
Root cause haar: theme-driven colors (MaterialSwitch, default buttons, cursor, ripple) brown #AB6543 chhilo — karon **light mode e AppTheme nei**, only values-night/AppTheme existed. Fix: values/themes.xml e LIGHT AppTheme add (same full md_theme mapping, parent Theme.Material3.DayNight.NoActionBar) + colors.xml colorPrimary/Accent/Dark/ControlHighlight/Normal green family. Abar kono widget theme-color mismatch na.
### Changes
- **themes.xml**: LIGHT AppTheme (full color mapping — mirror of night one, DayNight parent). Night AppTheme already existed. Now light+dark both green.
- **colors.xml**: colorPrimary=#4C662B, colorPrimaryDark=#394E1F, colorAccent=#4C662B, colorControlNormal=#4C662B, colorControlHighlight=#CDEDA3 (je brown gulo theme e remap kore dibe).
- **icons rewrite (accurate Material paths, single path, #FFFFFF)**: ic_copy (content_copy), ic_paste (content_paste — age path nokia holo), ic_telegram (correct plane), ic_github (standard octocat). Purono broken paths bad. Baki (ic_search/refresh/lock/notif/send/person/folder) as kotha classic correct ache.
- **main.xml**: card_hero ekhon margin 16dp side + 8 top (full-width flush na), hero_icon 56dp. Text fields **sobgulo uniform FilledBox** (uid/pass/path) corner 18dp same bg + **14dp gap** between (pass o FilledBox e change, age OutlinedBox). fab_rescan explicit iconTint onPrimaryContainer.
- **GuestCore.showPreviewDialog v2**: theme-colored preview — pretty JSON (org.json toString(4)) + **syntax coloring** via colorizeJson (keys=md_theme_primary, strings=tertiary, numbers=error, punctuation=outline, text=onSurface — sob theme token), monospace, body bg surfaceContainerLow, boxed scroll, title=file name, buttons COPY JSON / COPY PATH / CLOSE.
- **Haptic**: result row click e performHapticFeedback(CLOCK_TICK).
- JVM verified org.json toString(4) output clean for guest_account_info JSON. Compile EXIT 0 (full androidx+material+coordinator), all ids/drawables/colors matched, XML well-formed.
- NOTE: JSONArray import added to GuestCore (was missing). b.setMessage + setView both allowed in M3 dialog (message subtitle + colored body).
## ✅ UI v5 2026-08-19 — BATCH ACCOUNT INJECTION + hero collapse + INJECT NOW color fix
User want: ekta JSON file theke anek account batch e inject kora (uid + password at least), DONE/CANCEL button diye sequential flow, per-DONE save → /storage/emulated/0/ZeronGuest/ZeronGuest.json. Ar ki: card_hero scroll effect (content hero er niche diye jae — collapse animation chai), INJECT NOW color theme match na.
### Changes
- **3 new icons**: ic_check (check), ic_close (close), ic_upload (upload) — accurate Material single-path #FFFFFF.
- **main.xml**: `hero_chips` id hero chip row te. **btn_inject** → solid `md_theme_tertiary` bg + `onTertiary` text/iconTint (age tertiaryContainer, mismatch chilo). **New "Batch Inject" card** (inject o about er majhe): til_import_path (FilledBox 18dp, hint "accounts JSON path (optional)", helperText, startIcon ic_folder) + `et_import_path`; row = `btn_select` (SELECT FILE, TonalButton tertiaryContainer, ic_folder) + `btn_load` (LOAD PATH, TonalButton primaryContainer, ic_upload); `batch_status` TextView; `btn_done` (primary, ic_check, "DONE & SAVE", GONE default); `btn_cancel` (TextButton error, ic_close, "CANCEL BATCH", GONE default).
- **GuestCore.java** (v5 core, already in): `Account` class; `parseAccounts(text)` robust (array / {"accounts":[...]} / single obj; UID_KEYS uid/guest_uid/UserId/userid/com.garena.msdk.guest_uid/account_id/id; PASS_KEYS password/pass/pwd/guest_password/com.garena.msdk.guest_password/Password; Number→longValue; null/xxx/empty pass skip); `readTextFile(path)` (4MB cap); `saveAccountToZeronGuest(uid,pass)` → mkdir /storage/emulated/0/ZeronGuest, append to ZeronGuest.json `[{uid:6814761164,password:"XXX_LEGACY_..."}]` (uid long if \d+ else string), returns OK|path / ERROR|msg.
- **MainActivity v5**: batch refs (etImportPath, batchStatus, btnSelect/btnLoad/btnDone/btnCancel); batch engine — `accounts` List, `acctIndex`, `batchActive`; `importAccounts(text)`→`startBatch()` (btnDone/btnCancel VISIBLE, select/load disabled, toast count)→`loadAccount()` (etUid/etPass fill, batch_status "Account X / N — inject koro, then DONE & SAVE"); btnDone → saveAccountToZeronGuest(fields) → OK hole acctIndex++ → next account or `finishBatch`; btnCancel → finishBatch (hide btns, re-enable select/load); **SAF picker**: btnSelect → ACTION_OPEN_DOCUMENT, EXTRA_MIME_TYPES json/text, REQ_PICK=7001, `onActivityResult` → queryName (OpenableColumns.DISPLAY_NAME) + readUri (ContentResolver, 4MB) → import; btnLoad path e content:// hole readUri, nahole readTextFile; **hero collapse**: scrollView.setOnScrollChangeListener — f=clamp(scrollY/120): heroIcon scale 0.7 + alpha 0.8, heroSub alpha 0.2, heroChips alpha 0 + translationY -18dp (scroll korle hero collapse hoy, premium feel).
### Verification
- javac EXIT 0 (real androidx + material + android-34 + stubs NonNull/RestrictTo$Scope/LifecycleOwner/ViewModelStoreOwner/HasDefaultViewModelProviderFactory/fake R — RestrictTo stub add korte hoise).
- Fake R.java updated: +ic_check/ic_close/ic_upload, +hero_icon/hero_sub/hero_chips/scroll, +et_import_path/batch_status/btn_select/btn_load/btn_done/btn_cancel ids.
- All ids/drawables/colors matched, XML all well-formed (python).
- **ParseTest JVM**: sample1 `[{uid,password,account_id,...}]` → 4 accounts (uid long 6814761164, textUid string ok), {"accounts":[...]} → 2, xxx/null pass → 0, garbage → 0, single obj → 1. ✅ IMPORTANT: test e json.jar **android.jar er age** rakhte hobe — android.jar er org.json stub (RuntimeException("Stub!")) → silent empty return. Classpath order: out:json.jar:*jars*:android.jar.
- aapt2 host nai (Android) — fork build pipeline e validate hobe. User **in-app build (signed) korte hobe** — last actual build 13:49 MainBinding fail chilo (custom MainActivity diye fixed), ekhon v5 ready.
### Notes / decisions
- Batch flow (user "baki research kore improve koro"): DONE/CANCEL tokhon e visible jokhon batch active; save kori current etUid/etPass field (user ekhon ja dekhche); inject noya per-account state save na — user INJECT NOW then DONE.
- btn_inject solid tertiary #386663 white text — theme match.
- Future: ZeronGuest.json append conflict-free (JSONArray merge), SAF takePersistableUriPermission optional.
