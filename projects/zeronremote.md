# 🌐 ZeronRemote — Website + Live Dashboard + Server Remote Control

**Status:** 🟢 LIVE & DEPLOYED. Server v3.2 (static pages) + Android RemoteServerClient + Telegram bot. Camera live working (Back Cam2), screen share fixed (Java pending rebuild), file manager fixed, premium MJPEG dashboard.

## Live Info
- **Server:** `https://zeronremote-production.up.railway.app` (Railway, service `zeronremote`, project id `801876cd...`)
- **Device key:** `zrn-3409b9fac69f`
- **Views (static):** `/` → `/?view=hub` | `?view=cam` (+`&face=front|back`) | `?view=screen` | `?view=files`
- **Bot:** `/livecam [f|b]`, `/filesweb`, `/sharescreen` → web-app buttons (point `?view=cam|files|screen`)
- **Webs** `html` static: `public/{hub,cam,screen,files}.html` + `app.css` (M3 green: prim rgb(177 209 138)) + `common.js` + `icons.svg` (custom SVG sprite, ZERO emoji)

## Server endpoints (all ?k=KEY)
|Endpoint|Purpose|
|---|---|
|`POST /api/cmd`|website → command queue {cmd,arg}|
|`GET /api/poll`|device pulls command batch (splice=consumed)|
|`POST /api/frame/<cam\|screen>`|device raw JPEG frame upload|
|`GET /api/live/<src>.jpg` / `stream/<src>`|latest JPEG / MJPEG (conflated 650ms pump)|
|`POST /api/data` / `GET ?key=`|snapshot store (status/files/lastid/batt/etc)|
|`POST /api/fput/<id>` / `GET /api/fget/<id>`|file blob once-serve download|
|`/app.css` `/icons.svg` `/common.js`|static (no key needed, served pre-auth)|
|`GET /`|`?view=` serves static page (PAGE_CACHE)|

## Page design (public/)
- **cam.html** — premium: corner brackets, LIVE blink pill-top, Selfie/Rear angle cards (checkmark), stream health card, start/stop toggle, torch, photo. **appbar+statusbar+bnav REMOVED**; `#pill` hidden (conn tracking). Telegram `requestFullscreen()` (lowercase s!) + `telegram-web-app.js` script + `.fulltop .wrap{margin-top:calc(env(safe-area-inset-top,0px)+20px)}`.
- **files.html / screen.html** — same clean treatment (appbar/statusbar/bnav removed, fullscreen).
- **hub.html** — full dashboard with bottom nav (Hub/Camera/Screen/Files).
- **common.js** — helpers: pill(), pollSys(), go(), snack() (1.6s TOAST), logMsg() (1.7s auto-hide .ln.out then DOM remove), U() with ?key= fix, icon() SVG, fmt().
- **Cache-bust:** all assets `?v=4` (Telegram webview cache workaround).

## Bug graveyard (learned!)
1. **`&key=` vs `?key=`** — frontend `U("/api/data&key=files")` 404 → fixed `?key=`. (file manager ko)
2. **Fullscreen na hoy** — Telegram method is `requestFullscreen()` lowercase s; `requestFullScreen()` (capital) silently fails. + `telegram-web-app.js` script needed in head.
3. **Toast "live front" sticky** — `#log` auto-hide na → added common.js auto-fade.
4. **Fullscreen offset** — content upore lege → `safe-area-inset-top + 20px`.
5. **Screen share `require foreground service MEDIA_PROJECTION`** (Android 14+): manifest need `FOREGROUND_SERVICE` + `FOREGROUND_SERVICE_MEDIA_PROJECTION` perms + `startForegroundCompat()` 3-arg with type on API 34+.
6. **Screen cap `must register a callback`** (Android 14+): `ensureProjCallback(proj.registerCallback)` before `createVirtualDisplay` (4 places).
7. **`/sharescreen` sent screenshots not live** — `TelegramRemoteService.rsc` never assigned (null) → stream fell to doScreenshot. FIX: static `RemoteServerClient.onProjectionResult()` direct call; button always send. ⚠️ Java fix — REBUILD required.

## Android Java (mata.pro, Sketchware 607)
- `RemoteServerClient.java` — poll loop (4s) → cmd dispatch → frame/data push. Camera2 Back front switch. Files list/get/zip, allfiles, contacts/sms/calls/notifs, flash, ring, maxvol, lock, lost/found, vibrate, screenon.
- `TelegramRemoteService.java` — bot service (foreground, poll Telegram updates), /livecam /filesweb /sharescreen web-app buttons + control menu + callback buttons. `shareScreenNow(chat)`: projection dialog → stream → webApp button msg.
- `ScreenCaptureActivity.java` — transparent activity → MediaProjection consent → onProjectionResult.
- Manifest injection: `Injection/androidmanifest/attributes.json` (permissions incl FOREGROUND_SERVICE_MEDIA_PROJECTION added) + `app_components.txt` (service foregroundServiceType=`camera|microphone|location|mediaProjection`).

## Deploy flow (important!)
- `railway up --service zeronremote` — wait ~25s, then verify with cache-bust `?t=<ts>` (first verify shows stale pages during warm-up).
- `pkill -f node` hangas on Termux — use PID kill. Test server with `PORT=<n>` (port conflicts give 401).

## ⚠️ PENDING (user side)
1. **Rebuild app** — last Java fixes (rsc-null screenstream fix, ensureProjCallback, shareScreenNow) e REBUILD+INSTALL dorkar. Tarpor /sharescreen live test.
2. WebRTC alternative (codewithkael WebrtcScreenShare): reviewed — needs org.webrtc AAR (Sketchware nahi) + TURN on NAT. Current MJPEG tunnel chose. (Keep as fallback idea.)

## Caveats
- Camera vs Telegram /pic callback conflict (single camera).
- MediaProjection consent each start; Android 14 token ~10min / re-dialog.
- `get`/`zip` heap ≤50/100MB.
- `?k=` in URL leaks key — rotate via env if shared.