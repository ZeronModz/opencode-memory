# Learned — 2026-08-09 · Brutalist web template + DevZeron API v2

## Brutalism 2026 (research takeaways, applied)
- Tactile brutalism: sharp 0px geometry, thick solid borders, components feel "engineered", not floating.
- Typography as interface: viewport-scaled (vw/clamp) giant display type carries the design; kinetic/scroll type.
- Bento grid paradox: multi-col desktop grid must collapse to single column on mobile without DOM duplication (native responsive).
- Chromatic extremes: raw hues (red/yellow/green), no pastels. CSS noise/grain > WebGL for texture.
- Motion: 80-150ms snappy, state-snaps (button slams down on :active), prefers-reduced-motion mandatory.

## DevZeron Temp Gmail API v2 — endpoint fact sheet
- Base: https://dev-zeron-temp-gmail-api-v1.vercel.app
- Auth: `Authorization: Bearer key_xxxx` or `X-API-Key: key_xxxx` (key stored Firebase, AES-encrypted app passwords, Fernet).
- endpoint regions (from /api/info): auth(register POST/{email,pass}; key GET; revoke GET) · generate(dot|plus|mixed|custom/<tag>|batch/<count>?type=) · read(read/<email>?limit=&unread=1|readby/<email>/<text>|unread/<email>|count/<email>) · manage(delete|markread|markunread /<uid>) · system(health|info — public).
- Every response: `{status, message, Api_By, Tg_Channel, data?}`.
- Registers limit: 6/120s per-IP.

## Gotcha: register 500 for non-gmail
POST /api/register with non-@gmail.com email → 500 HTML (clean_email PermissionError unhandled in register()). Valid @gmail.com -> 401 JSON "could not be verified". Template must parse JSON + fallback to raw text (done).

## Template engineering
- JSON syntax highlighter must work on FULLY-ESCAPED text: match `&quot;` as quote delimiter (regex negative lookahead), not raw `"`.
- localStorage persisted config in plain object ({base,key}) under one key; module var S = stateNow() recompute after persist.
- VM smoke test pattern: vm.createContext with stub DOM elements (getElementById/createElement classList etc.) → run app.js → fire init → assert renders + run(). Works headless for static site logic (no browser needed).