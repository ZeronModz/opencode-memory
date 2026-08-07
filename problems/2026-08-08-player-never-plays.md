# Player never plays / stuck "Preparing player…" (TeraBox bot)

- Date: 2026-08-08
- Symptom: Online play button opens player page but video never plays; stuck at LOADING/Preparing; persisted across native, Video.js CDN, custom pages. "Download/browser" worked.
- Root causes (comprehensive):
  1. Resolved stream is often HLS/m3u8 (not mp4). Native `video src=m3u8` never loads on Android/WebView.
  2. Raw CDN dlink as video src fails (CORS / Referer / one-time link). Direct-from-TeraBox-in-TG-WebView broken.
  3. A "wait for full local download then serve" /lst design DID satisfy playback but waits the ENTIRE file -> user complained no online feel (needs incremental buffering).
  4. Adapter embed bug: base64 hls.js embed deleted UPSTREAM_HEADERS -> NameError -> probes err.
  5. Dockerfile ships only bot.py+player.py -> standalone hls.min.js file never on Railway (served 0B).
- Solution applied: same-origin adaptive engine — /type probe -> hls.js-thru-px-proxy (rewrites all segment URLs) OR /px-proxy@nativrange for direct mp4; hls.min.js base64-embedded; loading overlay hides on canplay/playing; user's own player design preserved. Production state AFTER this session: wait for user video result (bugs possible: iteraplay rate-limit, STAT files, HEVC codec -> moov/mdat transcode need).
- Files changed: player.py (PAGE=ZERON PLAYER + engineVideo + _probe_type + /px + /hls.min.js + _HLS_JS b64), bot.py (unchanged this round), Dockerfile unchanged (must embed).
- Verify: `/play?url=x&title=T` contains engine markers + `/hls.min.js` size 413181 + /type kovan.