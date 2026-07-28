# TPY (Telebot Python) Limitations & Features

## Date: 2026-07-27

## DISALLOWED
- `import` statements ❌
- `urllib.parse` module ❌
- `eval()`, `exec()` ❌
- System modules (os, sys, subprocess) ❌

## ALLOWED Built-in Globals (No Import Needed)
- `rawurlencode` — URL encoding ✅
- `encodeURIComponent` — URI component encoding ✅
- `decodeURIComponent` — URI decoding ✅
- `parse_qs` — Query string parsing ✅
- `base64` — Base64 encode/decode ✅
- `hashlib` — SHA256, MD5 hashing ✅
- `regex` / `re` — Regular expressions ✅
- `HTTP` — HTTP client ✅
- `json_dumps` / `bf_json` — JSON handling ✅

## Button Types Supported
- `callback_data` — triggers callback
- `url` — opens URL in Telegram browser
- `web_app` — opens WebApp (requires `{"url": "..."}`)
- `copy_text` — copies text to clipboard
