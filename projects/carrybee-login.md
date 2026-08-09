# 🚚 CarryBee Merchant OTP Login (Python)

## State: ACTIVE — working

## Location
`~/carrybee-login/carrybee.py`

## What it does
Terminal OTP login + registration system for CarryBee merchant panel:
1. Input phone → normalize (017... / +880... / 880...) any format
2. register (new: name + business) OR resend (existing)
3. Step diye OTP prompt (4 digit) — `resend` likhle ager OTP expire hoile resend hoy
4. verify-otp → SUCCESS / Invalid Otp retry loop
5. Saves JSON state → `carrybee_state.json`

## API map (live verified)
| Action | Endpoint | Body | Resp (key) |
|---|---|---|---|
| Register | `POST /api/v2/merchant/register` | `{"name","phone_number":"+880...","business_name"}` | `data.otp_prefix`, `data.otp_expires_at` |
| Resend | `POST /api/v2/register/resend-otp` | `{"phone":"880..."}` | fresh `otp_prefix` |
| Verify | `POST /api/v2/register/verify-otp` | `{"phone":"880...","otp":"1234"}` | success / `Invalid Otp` |

Base: `https://api-merchant.carrybee.com/api/v2` — Origin `https://merchant.carrybee.com` header.

## Key gotchas
- OTP field = 4 digits ONLY (prefix SMS dey na, backend e prefix jay na).
- OTP `"0000"` → Laravel `empty()` bug → "otp required". Nonzero digits use koro.
- Phone normalize: strip `+`, `0` prefix → `880...`. Register e `+880` pathaite hobe, resend/verify e shudhu `880`.
- Verification web page = Next.js RSC, `?otpPrefix=RVI&_rsc=...` — eita API nah, page render. Real OTP flow frontend e NextAuth `signIn("phone-verify")` → `/api/auth/callback/phone-verify`.

## How web OTP flow works (reference)
1. Client POST register → server SMS pathai, otp_prefix return
2. Redirect `/register/verification/{phone}?otpPrefix={prefix}`
3. User 4 digit dile → NextAuth signIn provider → backend verify
4. NextAuth session cookie `__Secure-authjs.session-token` set

## Future todos
- [ ] NextAuth full session flow (csrf → callback/phone-verify → session cookie) dite chale dashboard data scrape korar jonno
- [ ] `getMe`, businesses list API endpoint add
- [ ] GUI / menu (add → `auth.me:"/me"`, businesses:`/businesses` already in API_ROUTES)

## Source
HAR: `ProxyPin8-10_03_57_21.har` (160 entries — user's own registration, phone 8801886292755)
JS chunks analyzed: `1x43nwn_tutvs.js` (verify component + authServices), `2kokwsbh3706a.js` (API_ROUTES), `0skehyqr29y_b.js` (route builders)