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