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

### 2026-07-28 22:04 — Full Emoji → Symbol Replacement + Text Bolding
**Summary**: Removed all decorative emoji from entire bot JSON, replaced with clean text symbols (◆◉◎▣✕+◂▸i!), and converted all display text to mathematical bold Unicode.

**What changed**:
- Every `\U0001f...` emoji escape → `◆ ◉ ◎ ▣ ✕ + ◂ ▸ i !` symbols
- Plain ASCII display text (Menu, Back, Generate, etc.) → mathematical bold (𝐌𝐞𝐧𝐮, 𝐁𝐚𝐜𝐤, 𝐆𝐞𝐧𝐞𝐫𝐚𝐭𝐞, etc.)
- `/start` buttons: 🗿→◉, 🖤→(removed), 💸→◆
- `/zeron-vvvvvvvvvvvvvv` verification command: ❌→✕, ✅→◆, 🔄→◎, ⚠→!, ❗→!
- All callback_data, function names, Python logic preserved

**Critical fixes applied**:
- `handleNextCommand` preserved (word boundary regex, not simple str.replace)
- `nSelect` variable preserved (same reason)
- JSON validity maintained by using `json.load/dump` instead of string replacement
- Handled both `\UXXXXXXXX` escape text AND actual Unicode emoji chars

**Output file**: `/storage/emulated/0/Download/zeron-telebot.json` (79288 bytes, valid JSON)
**Script**: `/data/data/com.termux/files/usr/tmp/opencode/transform_json.py`

### 2026-07-28 22:XX — TBC Import Fix (CRITICAL)
**Problem**: TBC gave "Syntax Error: cannot assign to literal" — two causes:
1. Dict `=` vs `:` — my replace function used `=` but Python/TBC requires `:`
2. Actual Unicode bold chars in source code broke TBC's parser (though Python's compile() was fine)

**Fix**: 
- Preserve original dict separator (capture and reuse)
- Use Python `\UXXXXXXXX` escape text for bold chars (same format as original `/start` caption)
- Output: 76857 bytes, valid JSON, all code compiles in Python

### 2026-08-05 21:32 — Full Upgrade: Admin Panel + Bold Font + New User Notification
**Summary**: Rebuilt bot JSON (40 commands) — added full `/zeron-admin` panel, block/mute & maintenance guards on every command, broadcast, message-a-user, and a rich new-user notification. Applied math-bold font across all remaining plain text.

**What was added**:
- `@` handler (runs before every command): user registration into `users` list (Bot.saveData JSON), `started` flag preserved, global counter via `libs.Resources.globalRes('status').add(1)`, and admin notification with FULL user info + inline buttons (User Detail / Block / Message) — skips admins.
- `/zeron-admin` — panel hub (Statistics / Users / Blocked / Maintenance / Broadcast / Message User)
- `/zeron-admin-users` — paginated list (10/page, ◂▸ prev/next), shows blocked mark ✕
- `/zeron-admin-user` — full profile (name, ids, username, language, premium, joined, blocked?) + profile photo via `bot.getUserProfilePhotos` if available
- `/zeron-admin-user-block` / `-unblock` — adds/removes ID from `Bot.getData("blocked")` comma-list
- `/zeron-admin-blocked` — list of blocked users
- `/zeron-admin-maint` — toggles `Bot.getData("maintenance")` on/off (usage reply)
- `/zeron-bcast` + `/zeron-bcast-send` — broadcast to all users via `Bot.broadcast` (HTML, ignore errors)
- `/zeron-admin-msg-user` + `-send` — DM a specific user by ID
- **GUARD** prepended to every command (except `@`): non-admin blocked → "blocked" notice; maintenance on → "under maintenance" notice; both `raise ReturnCommand` before main logic
- `/zeron-menu` — Admin Panel button (bold ◆, `u == 5271912123`) appended to keyboard for admin
- Old new-user notification block removed from `/zeron-vvvvvvvvvvvvvv` (moved to `@`)

**Build script**: `/data/data/com.termux/files/usr/tmp/opencode/build_zeron.py`
- `process_code()` / `bold_content()` — bolds ASCII letters in display strings (text=/caption=/result=/dict "text"), preserves escapes, HTML tags, {placeholders}, symbols
- FIXED BUG: step-3 list rebinding (`cmds = [...]`) detached new commands from `data['commands']` → only 28 written. Fixed with `data['commands'] = cmds`.
- FIXED BUG: menu injection double-run → duplicate button. Made idempotent (regex-strip previous injection + `replace(..., 1)`), rebuilt from clean backup.
- `compile()` syntax check on every command; output validated via `json.loads`

**Verification**: 40 commands, all compile; `@` present; 9 admin + 2 bcast cmds; exactly 1 admin button in menu; vvvv notification removed; `started` key still saved in `@`; callback_data/getData keys preserved.

**Output**: `/storage/emulated/0/Download/zeron-telebot.json` (145 KB, valid JSON) — ready to upload to Telebot Creator.
**Next step**: upload JSON on TBC → test on Telegram (new user notif, block/unblock, broadcast, maintenance, profile photo).

### 2026-07-28 22:XX — Second Pass: Complete Font Consistency (CRITICAL)
**Problem**: First pass left ~47 display strings with partial plain ASCII. Button texts, messages, captions had inconsistent bolding.

**Root Cause**: Phrase-list approach was inherently incomplete — many display strings weren't covered.

**Solution**: Rewrote with `bold_str()` — an intelligent function that:
1. Finds ALL display strings via regex (`text=`, `caption=`, `"text"=`)
2. Bolds every English letter (A-Z a-z) inside them
3. Preserves {template vars}, <HTML tags>, \escape sequences, and symbols
4. Uses proper Python string pattern matching (handles escaped quotes)

**TPY Syntax Note**: TBC uses `"text"="..."` (equals sign) instead of standard JSON `"text": "..."`. Both patterns matched.

**State**: ✅ ALL display text now consistently bold. Ready for import.
