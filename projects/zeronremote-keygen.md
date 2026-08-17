# 🔑 ZeronRemote — Device Key System (Keygen / Multi-device Auth)

**Status:** 🟢 LIVE & DEPLOYED (v6.0). Protek device er jonno alada key, web page theke generate/set/revoke.

## Live Info
- **Page:** `https://zeronremote-production.up.railway.app/?view=keygen`
- **Master key:** `zrn-3409b9fac69f` (env `DEVICE_KEYS[0]`, page unlock er jonno dite hoy)
- **Project:** `/data/data/com.termux/files/home/zeronremote/` (server.js + public/keygen.html)
- **Registry:** `keys.json` (server root, SHA-256 hashed, git-tracked — deploy er sathe upload hoy)

## How it works
- Server `DEVICE_KEYS` env = master key(s); `keys.json` = dynamic generated keys.
- Auth: incoming `k` (or `mk`) key match kore → env ∪ keys.json hash registry. Both work.
- Key format: `zrn-` + 43 char base64url (crypto.randomBytes(32) = 256-bit).
- FULL key kokhono server e save hoy na — shudhu SHA-256 hash + mask (`zrn-xxx...xxxx`).
- Key reveal sudhu generate er somoy; page copy button, tarpor ar dekha jay na.

## API endpoints (master-protected)
|Endpoint|Purpose|
|---|---|
|`POST /api/keygen?mk=MASTER&name=X`|generate new key (2s rate-limit, genLock), returns `{ok,key,name}` once|
|`GET /api/keys?mk=MASTER`|list `{name, mask, at, online, last}` + master mask — online = `lastSeenByKey[hash]` <9s|
|`POST /api/keyrevoke?mk=MASTER&mask=`|revoke key (confirm dialog on page)|

## Page structure (public/keygen.html — applist pattern, Material 3 dark)
- Same design/colors: `--prim rgb(177 209 138)` green, top pad `fhead +100px`, `.wrap +182px`, fhead card.
- Master gate: password input → sessionStorage `zr_mk` → unlock.
- Generate card: device name → POST keygen → keyout (dashed amber border, copy btn, security warn).
- List: card per key (name, mask, last seen time, online check-dot, trash revoke).
- Security info card: 256-bit, SHA-256 hash store, reveal once, master-only, rate-limit.
- Poll 4s: `load()` re-fetch → diff check JSON → `fillList(j)` (skip full re-render so input not reset). `forceRender=true` after genKey to show keyout.
- Icons (icons.svg): +`i-key`, `i-shield`, `i-plus`, `i-trash`.

## Server code notes (server.js)
- `keyStore = loadKeyStore()` (keys.json) — persists, survives server restart, deploy er sathe git-theke update.
- `auth()` accepts `k` OR `mk` param; records `lastSeenByKey[bkHash(k)]`.
- `isMaster(k) = !DEMO && DEVICE_KEYS[0]===k` — DEMO mode (default "change-me") e master APIs block (401 bad key).
- 2s `genLock` rate-limit on keygen.

## Verify checklist (deploy-er por)
1. `?view=keygen` → page 200 (title "Device Keys")
2. `/api/keys?mk=BAD` → `{ok:false,error:"bad key"}`
3. `/api/keys?mk=MASTER` → `{ok:true,keys:[],master:"zrn-340...c69f"}`
4. keygen → new `zrn-...` key comes once; re-list shows masked only.
5. Revoke → removes instantly. keys.json updates.

## Known bugs / learned
1. **EADDRINUSE** test e: same port e purano server thakle new server fail kore — test port alada rakho (319x).
2. **Termux pkill/pgrep hang** — background test server kill korte `pkill -f node serve` somosya dite pare; alada port diye shesh kore tarpor deploy.
3. `lastSeenByKey` ekhon **hash-keyed** — earlier raw-key bug (online always false) fixed.
4. keys.json test data deploy-er age reset korte hobe (rm keys.json) — nahole test keys prod e thake.

## Future updates (bravo)
- Telegram bot theke key management enable (master command `/keygen`, `/keys`, `/revoke`) — currently page-only.
- Per-key access scope (kon key camera/files korte parbe) — currently all key = full access.
- Key expiry / auto-revoke after N days unused.
- Multi-device snapshot separation (currently snap global — device B poll korle device A er data replace hoy).
- Push generated key directly to Telegram (jate page na khulte hole keo bole dey).
- Revoke hole device side detect → auto unlink (device e currently known nai).

## Related
- Main project: `zeronremote.md` (web/server/bot full state)
- Session logs: `sessions/2026-08-17.md` (v6.0 keygen round at 05:0X)- Device code: `RemoteServerClient.java` / `TelegramRemoteService.java` (not affected by keygen — auth key device er config theke ashe)