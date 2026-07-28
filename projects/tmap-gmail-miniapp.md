# Tmap Gmail Mini App

## Status: Active (Built)

## Last Updated: 2026-07-28 23:XX

## Description
Telegram Mini App for Tmap Gmail bot. Brutalism design. Users can view their Gmail accounts, read inbox, generate new email addresses, and manage settings — all inside Telegram.

## Tech Stack
- Vanilla HTML/CSS/JS (no framework)
- Telegram WebApp SDK v6+
- Vercel (static hosting)
- API: `https://zeron-gmail.vercel.app/api/`

## Files (in `/storage/emulated/0/Download/tmp-gmail-miniapp/`)
- `index.html` — Main entry
- `css/style.css` — Brutalist stylesheet
- `js/telegram.js` — Telegram WebApp SDK wrapper
- `js/main.js` — App state, navigation, API client
- `js/components/profile.js` — Profile + accounts
- `js/components/inbox.js` — Inbox + search
- `js/components/compose.js` — Generate emails
- `js/components/settings.js` — Settings
- `vercel.json` — Vercel deployment config

## Features
- Bottom navigation: Profile, Inbox, Generate, Settings
- Telegram user info (avatar, name, username, premium status)
- Account list with detail (view inbox, copy, delete)
- Inbox viewing with pagination
- Email search
- Generate accounts: Dot Trick, Plus Alias, Mixed, Custom
- API password management (localStorage)
- Export accounts
- Tutorial / Developer info

## API Endpoints
- `api/accounts` — List accounts
- `api/read?email=X&page=N` — Read inbox
- `api/search?query=X` — Search emails
- `api/generate?type=X` — Generate account
- `api/export` — Export accounts

## Notes
- API password stored in localStorage
- All API calls go through `zeron-gmail.vercel.app`
- Brutalism design: pure black/white, 3px borders, monospace, yellow accent, no transitions
