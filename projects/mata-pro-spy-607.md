# Project: mata.pro — Telegram Remote Control (Sketchware Pro 607)

## Summary
Telegram bot diye phone remote control (anti-theft / find-my-phone / remote ops).
Platform: Sketchware Pro (daydream v7), package `mata.pro`, files in `.sketchware/data/607/files/java/`.

## Bot
- Token baked in `server.json`: `8125197063:AAHEcRBr-...` (@ZeronLogBot, VALID — curl getMe OK)
- Owner chat_id: 5271912123
- Dead token discovered 00:00 session (7684995533... → 401) → replaced, rebuild required.

## Signing
- OPPO/ColorOS build-er default test key (CN=Android) install block kore → CUSTOM KEY required.
- Current keystore: `release_key.jks` (alias devzeron, CN=DevZeron, pass `devzeron123`, PKCS12).
- Pipeline: Sketchware rebuild → `apksigner sign --ks release_key.jks --ks-key-alias devzeron --ks-pass pass:devzeron123 --key-pass pass:devzeron123 --out SBD_devzeron.apk SBD_release.apk`.

## Files (java/)
- `TelegramRemoteService.java` — foreground service + getUpdates long-poll. Commands: /loc /pic /pic front /selfie /video N /audio N /batt /ring N|stop /flash on|off /sms /ip /status /lock /admin /lockmsg /closemsg /lost /found /claim /burst N /apps /wifi /maxvol /sim /overlay /optsetup /help.
- `AutoStartReceiver.java` — BOOT/MY_PACKAGE_REPLACED/USER_PRESENT → service + perms + KeepAliveReceiver.schedule.
- `PermissionHelperActivity.java` — transparent activity runtime perms.
- `MyNotificationListenerService.java` — notif listener → route to owner; NPE fixed (extras null-guard, try/catch).
- `GetSmsAndCalls.java` — watchdog on sms/call/boot/screen + SIM change + boot notify + power alerts.
- `DeviceAdminReceiver.java`, `KeepAliveReceiver.java` (+ `files/resource/xml/device_admin.xml`).

## Key fixes (branch history)
- V2: 401 dead token, notif listener NPE, owner auto-claim, /lock /lockmsg /lost /status, persistence.
- V3: requirePerm per command, /diag /burst /apps /wifi /maxvol /sim, boot/sim/power alerts.
- **V4 (latest, 2026-08-15):**
  - Flash → CameraManager.setTorchMode (SDK≥23) + legacy fallback.
  - Photo → setPreviewTexture(SurfaceTexture) + retry loop, removed 6s block.
  - Video → setPreviewDisplay(Surface(SurfaceTexture)).
  - /lockmsg → auto open overlay settings + blink fullscreen.
  - /maxvol → + INTERRUPTION_FILTER_ALL (DND off).
  - KeepAlive 5min → 3min; boot also schedules keepalive.
- **V5 (2026-08-17):** `/lockmsg` overlay → modern draggable gradient bubble (edge-snap chat-head style), LIVE badge blink, programmatic LinearLayout (no XML), FLAG_LAYOUT_NO_LIMITS, NOT_TOUCHABLE removed for drag. Compile clean (29 classes). Fixed Sketchware "effectively final" error via `final fRoot/fLp` aliases in OnTouchListener → clean 61 class.
- **V5.2 (2026-08-17):** REWORK to user structure — `/lockmsg` inflates `R.layout.zeron` (files/resource/layout/) + `params007` LayoutParams (exact user code pattern), Service-safe LayoutInflater (getSystemService), drag + edge-snap advanced. Added drawables bg_bubble.xml, bg_badge.xml. Removed programmatic helpers. Manifest has SYSTEM_ALERT_WINDOW + FOREGROUND_SERVICE already. Compile clean (61 class).
- **V5.3 (2026-08-17):** `/lockmsg`+`/closemsg` Telegram fix — showAlertBanner full try/catch (inflate was outside → crash e no reply), /closemsg LockAlertActivity.finishAlert crash guard, **reboot-restore** (KEY_LOCKMSG prefs, onCreate restore 2.5s delay), static helpers `staticShowOverlay/staticRemoveOverlay/staticOverlayOn` for MainActivity button2 (Service context, background-safe), onStartCommand ACTION_SHOW/CLOSE_OVERLAY handling. Compile clean (62 class). MainActivity block-button2 e `TelegramRemoteService.staticShowOverlay(this,"msg")` call korte hobe (user manual).

## Build state
- Compile check: javac (android-34.jar + okhttp/okio, source 1.8) → 29 classes clean.
- APK builds: /storage/emulated/0/sketchware/signed_apk/SBD_devzeron.apk (8.34MB).
- House phone: OPPO CPH2817 (Android 16). Second: Samsung A05m.

## Setup on phone (after install)
1. /start or sms command (GetSmsAndCalls) → or open app.
2. Notification access ON (service connect → auto restarts service).
3. Battery optimization OFF (/optsetup or Settings).
4. Overlay permission (/overlay) gor /lockmsg popup.
5. Device admin (/admin) for /lock.
6. /diag to verify all perms/status.

## Known limits (Android 15/16)
- Boot e camera/mic FGS background-start restricted → one-time app open / notif access restart needed.
- Screen capture live stream impossible without MediaProjection consent.
- Boot-start "app not installed" fix = custom keystore (see Signing).

## Test checklist (after next rebuild+install)
- /flash on|off, /pic, /video 10, /lockmsg test, /ring, /sim, /batt, /apps, /wifi, /diag.- **V5.4 (2026-08-17):** `/lockmsg` fully programmatic bubble — R/XML/drawable dependency REMOVED (root cause: service e R.layout.zeron inflate/R.id resolve runtime crash → false → misleading "permission NAI" reply). Now: root LinearLayout + GradientDrawable programmatically (gradient #262C3F→#1A1F2E, corner dp20, stroke #3D4A66; LIVE badge #1F7A45, corner dp10), params007 + drag + edge-snap + startBlink. showOverlay() canOverlay() gate removed → always try + **actual exception** pathano Telegram e. addView catch → RuntimeException("addView fail: ..."). Compile clean (android-34 + okhttp + okio + old classes). User rebuild + sign + install → bash /lockmsg hi.
- **V5.5 (2026-08-17):** REAL fix — main-thread Looper. Actual error theke root cause: Telegram poll runs on `Thread[telegram-poll]`, WindowManager.addView/removeView main Looper chai → "Can't create handler inside thread...Looper.prepare()". `showOverlay()` now wraps whole body in `handler.post()` (main Looper). `removeOverlayAsync(chat, reply)` helper added for background-thread close paths (/closemsg, /found, do:found). removeOverlay() stays sync. Compile clean. **Build + install → /lockmsg hi works.**
- **V5.6 (2026-08-17):** Task-removed (recents swipe) bubble loss fix — root cause: removeOverlay() cleared KEY_LOCKMSG prefs, onDestroy→cleanupAll→removeOverlay chole prefs chole jaito, restart e bubble restore hote na. Now: removeOverlay() view-only (no pref clear), removeOverlayPermanent() = +pref clear (explicit close only), onTaskRemoved → immediate restore + keepalive/restart path e prefs thakay onCreate restore kaj kore. Compile clean. Rebuild+install → test swipe-after-lockmsg.
- **V5.7 (2026-08-17):** `/lockmsg` panel redesign → **FIXED top-center** (no drag). Title "Zeron Message Panel" (badge+LIVE) upore, message niche multi-line centered, status "/closemsg" hint. Width screenW-32dp, y=dp(60), TOP|CENTER_HORIZONTAL gravity. Drag/edge-snap listener removed. Text boro hole panel niche theke barbe. Compile clean.
