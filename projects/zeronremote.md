# 🌐 ZeronRemote — Website + Live Dashboard + Server Remote Control + Anti-Theft

**Status:** 🟢 LIVE & DEPLOYED. Server v3.2 (static pages) + Android Telegram bot + web control. Full remote control + anti-theft system. Latest: V8.0 (4 new web apps + background features fix).

## Live Info
- **Server:** `https://zeronremote-production.up.railway.app` (Railway, service `zeronremote`)
- **Device key:** `zrn-3409b9fac69f`
- **Views (static):** `/` → `/?view=hub` | `cam` (+`&face=front|back`) | `screen` | `files` | `sms` | `applist` | `contactlist` | `callhist` | `gallery`
- **Bot:** `/livecam`, `/filesweb`, `/sharescreen`, `/apps`, `/contacts`, `/callhist`, `/gallery` → web-app buttons
- **Web static:** `public/{hub,cam,screen,files,sms,applist,contactlist,callhist,gallery}.html` + `app.css` (Material 3 green, prim rgb(177 209 138)) + `common.js` + `icons.svg` (custom sprite, ZERO emoji)

## Server endpoints (all ?k=KEY)
|Endpoint|Purpose|
|---|---|
|`POST /api/cmd`|website → command queue {cmd,arg} (addCommand: empty arg → auto-split from cmd string)|
|`GET /api/poll`|device pulls command batch (splice=consumed)|
|`POST /api/frame/<cam\|screen>`|device raw JPEG frame upload|
|`GET /api/live/<src>.jpg` / `stream/<src>`|latest JPEG / MJPEG (conflated 650ms pump)|
|`POST /api/data` / `GET ?key=`|snapshot store (status/files/lastid/batt/apps/contacts/calllog/gallery/etc)|
|`POST /api/fput/<id>` / `GET /api/fget/<id>`|file blob once-serve download|
|`POST /api/data key=fsroot`+|fsroot-verified listing: match = (snap.fsroot === want path)|
|`GET /api/ls?path=`|serves listing only when fsroot match; stale error cleared on success|
|`GET /api/sys`|lastSeen/now (heartbeat + poll both bump)|

## Page design (public/, Material 3 dark)
- **cam.html** — premium: corner brackets, LIVE blink pill, Selfie/Rear cards, stream health, MJPEG fullscreen. No appbar/bnav.
- **files.html** — v4.4: header floating (env+82px), `<--toph:148px`, compact 560px centered, **dedicated scroll container**. wrap absolute top env+148px bottom 0 overflow-y:auto, body overflow hidden, touch-action pan-y, padding 0 14px 120px. Preview lightbox + bottom sheet (Send to TG /tgdl, Zip /tgzip, Download, Close). pollDone stops reload loop. Grid/list toggle, breadcrumbs, search, skeleton.
- **applist.html** — search, app name/pkg/version, permission chips (granted/denied/total), expandable perm list, letter avatars. Data via `/api/data?key=apps` (device buildAppsJson push).
- **contactlist.html** — search, initials avatar, Call/SMS buttons (send `/call` `/sendsms` cmds). Data `/api/data?key=contacts`.
- **callhist.html** — search + All/In/Out/Missed filter tabs, dur + time. Data `/api/data?key=calllog`.
- **gallery.html** — photo grid (104px cells), lightbox preview (via `/filepush` → blob → `/api/fget`), thumb 720px for >8MB, **Send to Telegram** (download = /tgdl → bot only). Data `/api/data?key=gallery`.
- **hub.html** — full dashboard bottom nav (Hub/Camera/Screen/Files).
- **sms.html** — v3.9: header 76px fixed, search 148px offset, full-page scroll.
- **common.js** — pill(), pollSys(), go(), snack(), logMsg(), U(), icon(), fmt(), esc().
- **Cache-bust:** assets `?v=` + pages `?t=<ts>`.

## Telegram bot commands (TelegramRemoteService)
- `/start /help /menu /status /diag /loc /sos /wipe yes /pic /selfie /video N /audio N /batt /ip /ring /flash /sms /vibrate /screenon /bright`
- `/lock /admin /lockmsg /closemsg /lost N /found /optsetup /overlay /claim /burst /apps /apps list /files /allfiles /zip /wifi /sim /shot /sharescreen /livecam /screenrec /filesweb /contacts /callhist /gallery /call /sendsms`

## Android Java (mata.pro, Sketchware 607)
### TelegramRemoteService.java (~3300 lines)
- Poll loop 2s → Telegram updates → command dispatch → owner check (1-min /start claim window, then protected).
- **Anti-theft:** reboot-detect (elapsedRealtime<3min → 6s auto status+SIM+loc), SIM-change → AUTO LOST MODE (Sim change=lock+photo+loc), 3-bar wrong PIN (MyDeviceAdminReceiver.onPasswordFailed → onUnlockAttack: photo+loc+lost+lock), `/sos` panic (ring 20s+flash+vibrate+screenon+lock), `/wipe yes` (device admin factory reset, 2-step confirm), lost mode scheduler (loc interval + screen-on photo).
- **Foreground:** startForegroundCompat — computeFgsType() = only GRANTED perms (Android 14+ SecurityException fix), startForeground(3-arg) API34+.
- **Background features:** ensureFgsForCapture() + releaseTorchCam() (flash's camera khule rakhe → "camera busy"), requestLocationUpdates API31+/singleUpdate fallback, 15s retry + 40s timeout.
- **sendSimInfo():** IMEI/phone/ICCID/IMSI/operator/network/state/signal. **sendWifiInfo():** speed/freq/BSSID/IP/gateway/nearby.
- File push to web: `/filepush` + pushFileToWeb + scaledBitmap (8MB+ → 720px thumb).
- sendMessageWithWebApp() web_app inline buttons.
- keep-alive: PollRunnable 45s startForegroundCompat re-post + heartbeat() /api/sys + KeepAliveReceiver exact alarm.
- Full device info: getDeviceInfo (brand/model/android), /diag.

### RemoteServerClient.java
- poll loop 2s → doFiles (fsroot ALWAYS posts + nearest-parent walk-up fallback), tgdl/tgzip (file/zip→bot, 60MB), heartbeat(), publishScreenFile, **pushData(key,value)** public, **publishBlob(name,file)**, webAppUrl(view,extra).
- Camera2 live frames cam/front/back.

### Other
- **MyDeviceAdminReceiver.java** — onPasswordFailed x3 → onUnlockAttack; onPasswordSucceeded resets.
- **AutoStartReceiver.java** — BOOT_COMPLETED + QUICKBOOT_POWERON + LOCKED_BOOT_COMPLETED + MY_PACKAGE_REPLACED + USER_PRESENT → schedule keepalive + startService + ensurePermissions.
- **KeepAliveReceiver.java** — setExactAndAllowWhileIdle (API31) / setAndAllowWhileIdle fallback, 3min.
- **PermissionHelperActivity.java** — CRITICAL runs incl ACCESS_BACKGROUND_LOCATION; allGranted → startService.
- **GetSmsAndCalls.java** — SMS forward + PHONE_STATE + SCREEN_ON/USER_PRESENT + boot/power events.
- **MyNotificationListenerService.java** — notif forward to bot.
- **ScreenCaptureActivity.java** / **LockAlertActivity.java**.
- Manifest injection: `Injection/androidmanifest/attributes.json` (all perms) + `app_components.txt` (services/receivers, foregroundServiceType=`camera|microphone|location|mediaProjection`, QUICKBOOT/LOCKED_BOOT actions).
- Full permission list: INTERNET, SMS (read/send/receive), CALL_LOG, PHONE_STATE, CAMERA, RECORD_AUDIO, LOCATION (fine/coarse/background), BOOT, FGS+CAMERA+MIC+LOCATION+MEDIA_PROJECTION+SPECIAL_USE, NOTIF_LISTENER, POST_NOTIFICATIONS, VIBRATE, FLASHLIGHT, WAKE_LOCK, WIFI, DEVICE_ADMIN, BATTERY_OPT, OVERLAY, WRITE_SETTINGS, MANAGE_EXTERNAL_STORAGE, SCHEDULE_EXACT_ALARM, FULL_SCREEN_INTENT, READ_CONTACTS, CALL_PHONE.

## Known bugs / learned (must-read)
1. **`&key=` vs `?key=`** frontend 404 → use `?key=`. ✅
2. **Fullscreen** — Telegram `requestFullscreen()` lowercase s.
3. **Store args** — addCommand auto-splits cmd string into arg (old device + new device both work, fixes files forever-loading).
4. **FGS type** — Android 14+ static type + ungranted perm = SecurityException → computeFgsType() dynamic.
5. **setExactAndAllowWhileIdle** — API31+ SCHEDULE_EXACT_ALARM chara SecurityException → canScheduleExactAlarms() fallback.
6. **Camera busy** — flashCam/open camera held → releaseTorchCam() before capture.
7. **MediaProjection consent** — ensureProjCallback before createVirtualDisplay; Android 14 token ~10min re-dialog.
8. **/pic /video background fail** — FGS camera type + release + preview params.
9. **Location background** — needs ACCESS_BACKGROUND_LOCATION + requestLocationUpdates (singleUpdate unreliable on API31+).
10. **Audio silent** — empty-file check + 3GP/AMR_NB fallback.

## Deploy flow
- `git add -A && git commit -m ... && git push` → `railway up --service zeronremote` → wait ~25s → verify `?view=applist` etc all 200.
- Test locally: `node server.js` then curl localhost:3000 (kill via PID, `pkill -f` hangs on Termux).

## ⚠️ PENDING (user side)
1. **REBUILD Sketchware APK** — sob Java changes (V7.0 anti-theft, V8.0 background fixes, new voice, pushData/publishBlob, /filepush, /sos, /wipe, /callhist, /gallery, /sim info, /wifi info) e REBUILD + SIGN + INSTALL dorkar. Web side already live.
2. **Setup ekbar:** app open → perm dialogs (BACKGROUND_LOCATION included) → `/admin` (device admin, anti-force-stop) → `/optsetup` (battery opt exempt) → auto-start ON (Xiaomi/Realme/Vivo).
3. Test web apps: /apps /contacts /callhist /gallery buttons.
4. Background /loc /pic /video /audio test after rebuild.

## Caveats
- Phone OFF → kono app e kaj kore na (Google Find My Device o na). Layer: reboot-detect auto-report.
- Camera vs /pic conflict (single camera).
- `get`/`zip` heap ≤50/100MB.
- `?k=` in URL leaks key — rotate via env.