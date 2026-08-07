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
