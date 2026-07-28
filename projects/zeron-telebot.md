# Zeron Telebot Project

## Overview
- **Type**: Telegram Bot (Temp Mail)
- **Platform**: Telebot Creator (TPY language)
- **Bot ID**: 27063625
- **API**: https://zeron-gmail.vercel.app/api/
- **WebApp**: https://inbox-six-neon.vercel.app/

## Files
| File | Location |
|------|----------|
| Main config | /storage/emulated/0/Download/zeron-telebot.json |
| Backup 1 | /storage/emulated/0/Download/bxzb/zeron-telebot.json |
| Backup 2 | /storage/emulated/0/Download/bxzb/bot_commands_27063625_20260727 (1).json |

## Change Log

### 2026-07-27 20:XX — View HTML → WebApp
**Problem**: View Html button showed "Coming soon" message
**Solution**: Changed /zeron-inbox-msg to build URL with query params + use `web_app` button
**Details**:
- Removed `import urllib.parse` (not allowed in TPY)
- Using built-in `rawurlencode()` instead
- URL: `https://inbox-six-neon.vercel.app/?from=...&subject=...&date=...&to=...&uid=...&body=...`
- /zeron-inbox-html reverted to fallback (no longer needed)

### TPY Limitations Found
- `import` statements are disallowed
- `rawurlencode` is a built-in global for URL encoding
- `encodeURIComponent` also available

## Commands Map
- `/zeron-inbox-msg` — Message detail view (has View HTML web_app button)
- `/zeron-inbox-html` — Fallback (unused now)
- `/zeron-menu` — Main menu
- `/zeron-inbox-acc` — Inbox by account
- `/zeron-inbox-page` — Paginated inbox
- `/zeron-generate` / `/zeron-gen-dot` / `/zeron-gen-plus` / `/zeron-gen-mixed` — Email generation
- `/zeron-accounts` / `/zeron-acc-info` / `/zeron-acc-detail` / `/zeron-acc-del` / `/zeron-acc-del-do` — Account management
- `/zeron-delete` / `/zeron-delete-view` / `/zeron-delete-do` — Message deletion
- `/zeron-search` / `/zeron-search-do` / `/zeron-search-view` — Search
- `/zeron-pass` — Set API password
- `/zeron-developer` / `/zeron-tutorial` — Info
