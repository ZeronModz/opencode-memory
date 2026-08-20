# TeraBox VIP Player WebApp (Telegram Mini App)

## Location
`/storage/emulated/0/Download/Telegram/y.html` (self-contained single HTML + Firebase)

## Stack
- HTML/CSS/JS + Telegram WebApp SDK + SweetAlert2 + Font Awesome
- Firebase Firestore v10.8.1 (`free-fire-94295` project)
- Auth: Telegram user id; fallback localhost query param

## Features (Ultimate Edition v2)
- Instant TeraBox player + share to global feed
- Ranks (Bronze→Diamond) with XP multiplier, perks
- **Ornate level frames:** Ember Ring / Starlight Silver / Royal Gold / Celestial Ice / Divine Aurora (buildAvatar helper, sob avatar site)
- **Bonuses:** Welcome bonus FIRST TIME ONLY (+20 XP, `bonusClaimed`) + Daily login (+25 XP, `lastDailyClaim`)
- Leaderboard (top 3 by XP), frame collection showcase + preview modal
- Ghost/multi-level privacy mode, follow/like with XP rewards
- Admin panel (users + API config) — user id `8708940588`

## Admin access (localhost)
`http://localhost:8000/y.html?user=8708940588&name=DevZeron`
Query-param override only works outside Telegram (`tg.initDataUnsafe?.user` empty).
Inside Telegram admin auto-detected by ADMIN_ID.

## Hosting (local)
```bash
bash /storage/emulated/0/Download/Telegram/serve.sh   # start/restart server
```
- `python3 -m http.server 8000 --bind 0.0.0.0`, workdir MUST be the Telegram folder
- LAN IP: 192.168.0.100

## Gotchas / Learnings
- Storage FS (`/storage/emulated/0`) FAT-like → `chmod +x` kono kaj kore na; script `bash` diye chalaite hobe
- http.server vul directory theke chaliye 404 dey — `pkill -f "http.server 8000"` kore thik folder theke start
- `@property --ga` conic rotate — Chromium webview e support kore
## v6 TITAN Edition rebuild (2026-08-20) 🏗️
**Goal:** ager version er features + NEW: 12-level rank system, frame collection gallery, name styles, badge gallery, friend frames, spin wheel v2, achievements.

**Final file:** `~/terabox-vip/y-v6.html` (169KB) — hosted at `http://localhost:8080/y-v6.html`
**Parts:** `~/terabox-vip/build/` (part0a/0b CSS, part1a/1b/1c HTML, part2.js core, part3.js logic)

### Architecture (NEW v6)
- **RANKS**: 12 levels (Bronze Ember → Eternal Divinity), each with multiplier, verified badge (auto at Lv2+), frame, color
- **FRAMES**: 12 rank frames + 5 extra (neon/galaxy/magicstar/royalwings/omega — spin count unlock) + 1 admin exclusive (DevZeron Officer)
- **NAME_STYLES**: 8 (gold/silver/flame/purple/neon/royal/infinity/crimson) unlocked by level
- **ACHIEVEMENTS**: 12 (shares/likes/follows/xp/spins/frames/level base), claim → XP
- **SPIN v2**: 8 slices, pity counter (7 spins → guaranteed jackpot 65xp), booster (2x 24h), spin log, daily 1
- **FRIEND FRAMES**: follow kora user er frame nijer profile e apply
- **Admin**: users CRUD (edit name/xp/private), API base URL, DB reset (loop-delete videos+users), my-account reset
- Firestore collections: users, videos. Rules: open read/write required for reset

### Verify commands (rebuild ke kokhonoi ase)
```bash
cd ~/terabox-vip/build && node --check module.js   # syntax
node harness.mjs                                   # boot sim: 34/34 handlers + flags PASS
```

### Gotcha (learned hard way)
- **temp folder wipe**: build parts temp e rakha → clean hotey paro. ALWAYS persistent `~/terabox-vip/build/` use koro
- **python splice cut bug**: `.replace` er sathe `.find` combo e big chunk cut hote pare — verify `wc -c` after edits
- regex er moddhe `/s\//` backslash HTML heredoc-e haraay — `node --check` protibari
