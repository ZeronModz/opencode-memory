# 🚚 CarryBee Master API (Vercel)

## State: ACTIVE — deployed live

## URL
`https://carrybee-master-api.vercel.app`

## Usage (GET)
```
https://carrybee-master-api.vercel.app/?num=01886292755&mes=ZERON
```
- `num` = phone number (017... / 880... / +880... sob normalize hoyeche)
- `mes` = business name (default ZERON)
- Name jekono request e RANDOM generate hoy (`Names[]` + 4-digit suffix)

## Response (success)
```json
{
  "status": "success",
  "developer": "DevZeron",
  "telegram": "t.me/DevZeron",
  "GitHub": "https://github.com/ZeronModz",
  "message": "Message Sent Successfully",
  "name": "...", "business_name": "...",
  "otp_prefix": "...", "otp_expires_at": "..."
}
```

## Flow / Logic
1. normalize_phone → register POST `/api/v2/merchant/register` `{name, phone_number:"+88...", business_name}`
2. 422 `phone_number taken` → fallback resend `/api/v2/register/resend-otp` `{phone}`
3. 429 "Daily limit exceeded" → branded error message (CarryBee per-number anti-spam)
4. Response always branded + CORS `*`

## Deploy notes (Vercel)
- `~/carrybee-master-api/` structure: `api/index.py` + `vercel.json`
- `vercel.json`: shudhu rewrites `{source:"/(.*)",destination:"/api"}` (root serve er jonno) — **functions/runtime block na** (version error).
- Python 3.12 (uv build), **zero dependency** (stdlib urllib), BaseHTTPRequestHandler pattern.
- `urllib` HTTPError → body read korte hobe (`e.read()` parse), nahole "HTTP Error 422" generic.
- Login: `node .../vercel/dist/vc.js` (Termux shebang issue → direct node path), user `zeronmodz`.

## Known limits
- `8801886292755` (user's demo number) — 2026-08-09 e daily OTP limit exhausted (repeated tests). Fresh numbers e register works.
- OTP verify (`/register/verify-otp` `{phone, otp}`) — terminal script e ache, master API te nei (kintu add kora easy).

## Source
Base script: `~/carrybee-login/carrybee.py` (HAR reverse-engineered API map er moto)