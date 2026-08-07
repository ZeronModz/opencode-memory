# HLS/direct Telegram WebView streaming (TeraBox) — 2026-08-08

## Core truth (why "first version worked" but later didn't)
- A "stream link" for TeraBox can be a DIRECT mp4 dlink OR an HLS/m3u8 manifest (iteraplay `fast_stream_url`, mn-bots `media_url`). NEVER assume one kind.
- HTML5 `<video src=HLS>` on Android Chrome/WebView does NOT play m3u8 without hls.js (or native only on iOS/Safari).
- Serving CDN dlink directly as video src fails in Telegram WebView for CORS/Referer/one-time-link reasons.

## Bulletproof recipe (proven)
1. Player page must load ONLY same-origin URLs. Never the raw CDN dlink.
2. `/type` route probes the upstream (first 2KB) -> kind: hls|direct|err.
3. `kind=hls` -> hls.js `xhrSetup: (x,url)=> x.open('GET','/px?url='+enc(url))` rewrite EVERY request (manifest + segments + keys) through own proxy => same-origin, no CORS, session cookies applied. Auto-play muted fallback when user-gesture blocked.
4. `kind=direct` -> `video.src='/px?url='+enc(dlink)`; /px forwards Range (native seeking/206).
5. Hide "LOADING…" on canplay/playing; buffer % pill from video.buffered (works with 206).
6. Loader/UI = user's own design (their HTML mounted as PAGE); only the engine is injected.

## Embedding gotchas
- Railway Dockerfile here COPYs ONLY bot.py+player.py -> any helper file (hls.min.js) must be base64-embedded into player.py or Dockerfile modified. Embed as `__import__("base64").b64decode("....")`.
- Weigh: after any base64 embed, recompile + run local smoke test (start server, curl /type /px /play). My first embed silently EAT a whole UPSTREAM_HEADERS dict (between UA and STREAM_COOKIES) -> NameError -> probes all came back "err". Grep `_up_headers` after edits.

## Useful public probes (env-blocked vary)
- HLS test: `https://devstreaming-cdn.apple.com/videos/streaming/examples/img_bipbop_adv_example_fmp4/master.m3u8`
- MP4 test (sometimes geo..): `https://www.w3schools.com/html/mov_bbb.mp4` (works), googleapis BigBuckBunny gave 403 on Termux/Telegram tunnel.

## Deploy
`railway up --yes --service TeraBox69xBot` = fresh upload (redeploy reuses old build). Verify: `curl /play` page + `curl /hls.min.js -o /dev/null -w "%{size_download}"` should be ~413181.