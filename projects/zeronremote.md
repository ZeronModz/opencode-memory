# ZeronRemote Project

## Overview
- **URL**: https://zeronremote-production.up.railway.app
- **Source**: `/data/data/com.termux/files/home/zeronremote-master/`
- **GitHub**: `github.com/ZeronModz/zeronremote`
- **Deploy**: Railway (auto-deploy on push)
- **Package**: Node.js zero-dependency server

## Files
- `server.js` — Main server (~283 lines)
- `public/` — All web panels
  - `hub.html` — Dashboard hub
  - `gallery.html` — Photo gallery with thumbnails + lightbox
  - `files.html` — File manager
  - `cam.html` — Camera streaming
  - `control.html` — Remote control (accessibility)
  - `sms.html` — SMS viewer
  - `applist.html` — Installed apps
  - `contactlist.html` — Contacts
  - `callhist.html` — Call history
  - `common.js` — Shared helpers (go(), pollSys(), U(), esc(), fmt())

## Gallery Panel (2026-08-25)
- Grid shows base64 thumbnails (`th` field from device)
- Lightbox: shows thumbnail immediately + polls for full image (14 retries, 1.5s)
- Send to Telegram: polls status for confirmation (8 retries, 2s)
- CSS: thumbnail fades in, overlay buttons z-index for clickability

## Server API Endpoints
- `GET /api/poll?k=KEY` — Device polls for commands
- `POST /api/cmd?k=KEY` — Web sends commands `{cmd, arg}`
- `GET /api/data?key=X` — Read snapshot
- `POST /api/data?k=KEY` — Device posts snapshot `{key, value}`
- `POST /api/fput/<id>` — Upload file blob
- `GET /api/fget/<id>` — Download file blob (one-time)
- `GET /api/ls?path=` — File listing
- `GET /api/sys` — System status
- `GET /api/devices` — List devices
- `POST /api/select?device=` — Select active device
