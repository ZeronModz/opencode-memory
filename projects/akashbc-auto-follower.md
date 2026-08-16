# AkashBc Auto-Follower (multi-account)

## Location
`/storage/emulated/0/Download/AkashBc/`
- `app.py` — main script (updated 2026-08-16, JWT now own local API)
- `access.json` — 636 accounts
- `follow_pb2.py` — proto definitions (CSFollowReq/CSFollowRes)
- `jwt/` — own Flask JWT API (app.py, my_pb2.py, output_pb2.py), runs on port 1080

## Purpose
Free Fire (Craftland?) account theke JWT niye target follow request pathano. Multi-account batch process.

## Flow (after 2026-08-16 update)
1. Prompt account file path (default `access.json`, CLI arg accepted) + target UID (default `7842525752`).
2. Load all accounts -> extract `uid` + `password` only (extra fields ignore).
3. Per account:
   - **Local JWT API (auto-started)**: `http://127.0.0.1:1080/token?uid=<>&password=<>` → fresh JWT. `app.py` nije `jwt/app.py` (Flask, port 1080) spawn kore; normal end / Ctrl+C e close kore. Age tao chile reuse.
   - POST `https://clientbp.ggpolarbear.com/Follow` with `Authorization: Bearer <token>`, AES-CBC encrypted protobuf.
   - Show token expiry countdown + follow result (capacity / fail_info).
4. Summary: total / OK / failed + failed UID list.

## API Notes
- Token API: 429 `{"code":1006,"error":"error_too_many_requests"}` possible — retry logic covers.
- Follow success even with `fail_info: BR_WORKSHOP_ALREADY_FOLLOWED` (already followed target).
- `remaining_follow_capacity` ~47 per account server-side limit.

## Key encrypt params
KEY/IV static bytes (CBC), protobuf req target_id, Unity headers + X-Ga v1, Releaseversion OB54.

## Run
```bash
cd /storage/emulated/0/Download/AkashBc && python3 app.py
```
Deps: requests, colorama, pycryptodome, protobuf.

## History
- 2026-08-16: single-JWT → multi-account JSON file + fresh JWT refetch + dynamic color UI (session 2026-08-16).
- 2026-08-16 V2: true follow detection (response `info` = FOLLOWED, `fail_info` = NOT FOLLOWED) + `history/` folder e per-run JSON (run_YYYYMMDD_HHMMSS.json: tokens, status, responses, summary).
- 2026-08-16 V3: vercel-hosted token API (vinnyyy-tools, rate-limited) → **own local Flask JWT API** (`jwt/app.py`, port 1080) auto-spawned per run; no hosting needed. `PyJWT` installed.

## Follow Success = Check
- HTTP 200 NOUCHIT na. Proto parse → `info` present & no fail_info → FOLLOWED.
- `fail_info` (e.g. `BR_WORKSHOP_ALREADY_FOLLOWED`) → NOT FOLLOWED (counts as failed).