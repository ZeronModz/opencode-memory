# TeraBox Downloader Bot (Railway)

- Date: 2026-08-07
- Path: /storage/emulated/0/my-projects/terabox-downloader/
- Hosting: Railway project `TeraBox69xBot` (project id 74f78017-72e3-473d-b8bd-3995f9685871, service id 95ff2417)
- Bot token env: `BOT_TOKEN` set via `railway variables --set "BOT_TOKEN=..."`
- Language: python-telegram-bot

## Current state
- bot.py v5 DM-only downloader, telegram bot polls via railway.
- railway.json startCommand: `python3 bot.py`; Dockerfile has ffmpeg.
- Admin set: 5271912123 (bot_data.json)
- FREE_DOWNLOADS=5, then invite = 4h unlimited (env overridable).

## TeraBox download fix (2026-08-07)
- Old API `https://terabox.beer/api/terabox-new` returned dead/worker-fallback, link 404/1101 -> "TeraBox download failed".
- Switched to free worker `https://terabox-api.mn-bots.workers.dev/download?url=<enc>` returning `media_url` = HLS m3u8 subject to ffmpeg.
- Implemented retry+validation: `_resolve_host` retries up to TERABOX_STREAM_RETRIES=15, requires playlist >=2 HTTP segments (#EXTM3U) before accepting.
- Verified multi-seg m3u8 -> valid .ts segments fetchable -> ffmpeg -c copy works.
- fn `get_terabox_stream` -> (m3u8, filename); `download_terabox` -> ffmpeg, re-encode fallback.
- Deployment: `railway up --detach --service TeraBox69xBot` (use --service when >1 service).

## Reality / caveats
- Public free TeraBox APIs are FLAKY / often down (TeraBox breaks them often). mn-bots ~3/4 up at one point then 0/10 down.
- Official `terabox.com/share/list?app_id=250528` returns errno 105 (requires login cookie, ndus).
- Robust long-term fix: provide a free TeraBox `ndus` cookie (browser devtools) and use cookie-based official-api/worker resolver. Not yet implemented.
- Railway filesystem is EPHEMERAL: bot_data.json downloads/ reset cada restart. Could add volume/in found a reliable provider.

Future todo: cookie-based reliable resolver + railway volume for bot_data.json.
## Big fix: iteraplay.com provider (2026-08-08)
- Found drdevstudio/Terabox-Video-Downloader-and-Stream- repo: THEY scrape iteraplay.com via POST /api/download, rotation cookies+proxies.
- Discovered iteraplay works with PLAIN requests: GET https://iteraplay.com/ (warmup) => session_id + __secure_token cookies => POST /api/download {"url":link} => JSON with fast_stream_url (quality m3u8 dict) + normal_dlink.
- No login cookie/proxy  needed for few calls. Rate limit = usage_limit (429) per IP -> rotation via proxies needed for heavy use.
- Integrated _itera_resolve() as PRIMARY in bot get_terabox_stream (3 tries) -> fallback mn-bots worker hosts (_resolve_host with validation retry).
- UA changed to Windows Chrome 124. no new deps (requests only). Deployed.
- Caveat: Railway datacenter IP may get Cloudflare block on iteraplay; mn-bots fallback covers intermittent. For heavy production, add proxy rotation like the repo.

## Admin v2 + redeem/gift + player fix (2026-08-08)
- PLAYER fix: custom-controls page stalled -> rewrote player.py PAGE to NATIVE video controls (guaranteed gesture playback) + modern light/glass design, poster=real ffmpeg frame (/thumb), buffering overlay %, top brand, DOWNLOAD for me button, @DevZeron credit. server_version=TeraUltra/1.0.
- DOWNLOAD-IN-BOT fix: was URL-document (Telegram server refetch, flaky). Now send_dl_cb streams direct link to temp file (disk-safe _download_stream) then sends real file via local API. Removed double use_download (card already counts).
- FREE PLAN adjustable: DB cfg.free_downloads; free_quota()/save_free_quota(); eff_quota()=free+bonus_downloads. All quota text now eff-aware.
- User model: bonus_downloads, redeemed added.
- Admin v2 panel (/admin): STATS, USERS (paged clickable -> per-user: +1/+5/+20 dl, UNLIMITED duration 2h/12h/24h/3d/7d/forever, BAN/UNBAN/RESET), GRANT (type id), REDEEM (gen/list/revoke), GIFT (gen/list), FREE PLAN (-1/0/+1/+5/exact), SEARCH (full user info), BROADCAST, INFO.
- REDEEM code: /redeem <CODE>; admin gen format `maxuses,hours_valid,extra_dl,unl_hours` e.g. `20,24,10,0`; expiry time-based; per-user once; tracked DB.redeem.
- GIFT link: admin gen `max_claims,extra_dl,unl_hours`; users claim via t.me/<bot>?start=gift_<token>; distinct claimants enforced DB.gift.
- Deployment commands (MUST use fresh upload, redeploy reuses old build):
  `railway up --yes --service TeraBox69xBot` from project root.
- 409 Conflict race on swap is transient (old replica dies); verify with `railway logs | grep getUpdates 200`.
- 2026-08-08 01:57 two deploys done (builds 727af422..., c7e7de87...); new code LIVE (getUpdates 200, admin callbacks 200).

## Player still-not-playing + download hang fix (2026-08-08 02:4x)
- Root cause likely: iteraplay dlink requires session cookies; our player proxy + in-bot download were STATELESS -> Cloudflare/TeraBox hang.
- player.py: added STREAM_COOKIES dict + _up_headers() (injects Cookie header). Used in /v /dl /meta /thumb + ffmpeg -headers Cookie.
- bot.py: _itera_resolve() now pushes its session cookies into player_server.STREAM_COOKIES after warmup. _download_stream() also sends those cookies.
- Player overlay .ov now pointer-events:none so it never blocks native video controls.
- Admin panel redesigned to classic emoji + 2-buttons-per-row + fancy fonts. Planned -5 button added.
- REDEEM/GIFT GENERATE now insta (one-click default): redeem = 5 uses/6h/+3dl; gift = 20 claims/+3h unlm + returns real t.me link. Custom typing via 🎛 custom: redeem `mUses,hours,extra_dl,unl_hours`; gift `claims,extra_dl,unl_hours`.
- New helper _make_code(prefix,n). Deployed build 79d78953 (getUpdates 200 OK).

## Player rework ~everything 03:1x-05:0x (2026-08-08) — FINAL WORKING STATE
- ROOT CAUSE of "video never plays across every player": resolved stream can be EITHER direct mp4 dlink (iteraplay normal_dlink/dlink/direct_link) OR HLS m3u8 (fast_stream_url / mn-bots media_url). All players before fed ONE type -> always stuck on the other.
- BACKEND JS CLASSIC: feed browser a SAME-ORIGIN url only (proxied), never raw CDN dlink.
  Routes in player.py:
  - /type?url= -> probe first 2KB w/ _up_headers -> {"kind":"hls"|"direct"|"err","size","type"}. HLS = body starts #EXTM3U; direct checked not starting "{".
  - /px?url= -> passthrough proxy (_proxy_out) for m3u8 text + segments + direct file (+ Range forward for mp4 seeking). Same-origin = NO CORS.
  - /hls.min.js -> serves hls.js 1.5.13 (413181 bytes) BASE64-EMBEDDED in player.py (line _HLS_JS = b64decode...). Dockerfile ONLY ships bot.py+player.py so separate files never reach Railway (that's why a standalone hls.min.js file served 0B).
  - /dl = browser full download proxy; /lst+/st+parallel segments removed from use (leftover).
- BOT passes DIRECT/STREAM URL as /play?url=<direct_url>&title=<fname> (quote'd, enc). Player PAGE boot(decoded url) -> engineVideo(v,url):
  kind=hls -> Hls.js (xhrSetup rewrites EVERY request /px?url=<original>) attachMedia -> incremental buffered play (YouTube style);
  kind=direct -> v.src=/px?url=<url> native + full; autoplay muted fallback.
  final.hide loader on canplay/playing/error -> show. Buffer strip % from video.buffered.
- PAGE (2026-08-08 05:5x, USER'S OWN DESIGN "ZERON PLAYER"): Archivo Black/JetBrains Mono, black/yellow neo, school-frame style, grid bg, thick 6px frames + offset shadows, credits @CodeDevZeron/@DevZeron, Telegram MiniApp SDK fullscreen + safe-area + disableVerticalSwipes, long-press/contextmenu/haptic disabled via user-select:none + contextmenu/selectstart/copy/dragstart prevent + [hidden] attr pattern, No URL? -> landing with manual url/title inputs. Player = Video.js v10 (@videojs/html cdn) with video-player/video-skin wrappers, plus our engine (engineVideo) + loader (loading-bars) + BUFFERED strip.
  Local </script> additions by me: engineVideo(fetch /type -> hls/direct through /px + hls.js) + local /hls.min.js include + fallback CSS (.player-mount video{width:height ...}).
- Deploys: df829680 (adaptive), 24cbef76 (ZERON PLAYER v1), 9da0ad08 (v3 w/ buffer-strip+long-press block). LIVE verified: /play markers + /hls.min.js 200 413181B.

## Embedded-base64 DEBUG BUG (lesson)
- python embed script replacing try/open block ate UPSTREAM_HEADERS dict (UA..STREAM_COOKIES) -> NameError -> every probe err/403. Always recompile + local smoke probe after splicing big base64. Local selftest (PORT=879x) validated: /play 200 page; /type hls->application/x-mpegURL; /type mp4->direct 788493 video/mp4 (googleapis 403 geo block; w3schools OK); /px hls #EXTM3U text; /px mp4 full ftyp bytes; cat . FIXED.
- ALSO wildcard: at /proxy err probe first 2K read.

## STILL TODO / known risk
- /dl browser button proxies RAW url (m3u8 -> downloads playlist text not video). In-bot download (send_dl_cb) handles HLS via ffmpeg _download_stream. If user needs browser-download to give MP4, /dl must be HLS->mp4 merge/transcode route (ffmpeg pipe or segment concat).
- iteraplay rate-limit 429 per IP; Railway datacenter IP Cloudflare risk; cookies injected via STREAM_COOKIES after warmup.
- Railway FS ephemeral (bot_data.json resets). Not volumed.
- Dockerfile only COPY bot.py+player.py -> all new supporting files MUST be base64-embedded in player.py or added to Dockerfile COPY.

## DEPLOY (MUST)
- From /storage/emulated/0/my-projects/terabox-downloader: `railway up --yes --service TeraBox69xBot` (fresh upload; redeploy reuses old build). Verify via curl player page + /hls.min.js size.
MD