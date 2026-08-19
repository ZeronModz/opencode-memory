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