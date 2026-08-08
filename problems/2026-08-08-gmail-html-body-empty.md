# Problem: Temp Gmail API body empty for HTML-only emails

## Date: 2026-08-08
## Project: dev-zeron-temp-gmail-api

## Problem
- `GET /api/read/<email>` response e HTML body wala email gulo te `"body": ""` ashe, body show kore na.
- Projects: TeraBox, OTP emails etc (mostly HTML-only, no text/plain).

## Root Cause
- `body()` in `api/index.py` only looked for `text/plain` MIME parts.
- HTML-only messages have no `text/plain` part → returned "".

## Solution
- `body()` now: prefers `text/plain`; if none → `text/html` part, HTML stripped via `_strip_html()` (regex + unescape) → readable text.
- New `body_html` field in message_data returns raw HTML for clients that need it.
- Added helpers `_walk_parts()`, `_decoded_part()`, `body_html()`; import `html.unescape`.

## Files Changed
- `api/index.py`

## Verification
- Locally constructed HTML-only message → body yielded text with OTP preserved; plain & empty cases unchanged; py_compile OK.