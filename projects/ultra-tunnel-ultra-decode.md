# 🔐 Ultra Tunnel `.ultra` config decoder (project)

## Target
Decode encrypted VPN config files exported by **Ultra Tunnel VPN**
(`co.strongteam.ultra`, MIME `application/x-ultra`, ext `.ultra`).

File: `/storage/emulated/0/Download/🇧🇩 BD সার্কেল এবং রবি একদম আনলিমিটেড ফ্রি.ultra`
(2017 bytes; dup in `/storage/emulated/0/Download/Telegram/`)

## Status: ✅ DONE (2026-09-27)

## Source of truth
- Native lib: `libgojni.so` (Go 1.24.3 gomobile, `github.com/2dust/AndroidLibXrayLite` fork,
  private repo `Ultra Tunnel/AndroidLibXrayLite v2.5`)
- Java side: `NetworkAdapter.java` (export), `ConfigFileParser.java` (import),
  `ConfigUtil.java` (per-field encrypt via `Libv2ray.eo`),
  `Hometab.Companion.a()` → `Libv2ray.do_` (decrypt)

## Algorithm

### A. Outer file → `Encrypt` / `decrypt`
```
pw   = "/4ssU0OjjtKH8AheiAxnc3DwSGDzsy+8"   (32 B, .rodata @0x366351)
key  = argon2id(pw, salt=blob[0:16], t=3, m=8192 KiB, p=1, len=32)
blob = salt[16] || nonce[12] || AES-256-GCM(ct||tag)   ; AAD = salt
file = base64(blob) with ONE random char inserted at index 3
```
Decrypt: `s[:3] + s[4:]` → b64decode → salt=b[0:16], nonce=b[16:28], ct=b[28:]
→ argon2id → GCM Open with AAD=salt.

### B. Inner fields → `EO` / `DO`
```
pw = "NurChickenPowder"   (16 B, .rodata @0x34bf00)
same argon2 + GCM + AAD=salt + blob layout, NO random-letter insertion
```
Applies to: `Payload`, `SNI`, `V2rayAddress`, `V2rayConfig`, `V2rayHost`, `V2raySNI`.
Other fields (`Name`, `Info`, `Icon`, `Route`, `TunnelType`, ports, flags) are **plaintext**.

## Decoder
```python
import base64, json
from argon2.low_level import hash_secret_raw, Type
from Crypto.Cipher import AES
PW_OUTER = b"/4ssU0OjjtKH8AheiAxnc3DwSGDzsy+8"
PW_INNER = b"NurChickenPowder"
def gcm_dec(b, pw):
    salt, nonce, ct = b[0:16], b[16:28], b[28:]
    k = hash_secret_raw(pw, salt, 3, 8192, 1, 32, Type.ID)
    c = AES.new(k, AES.MODE_GCM, nonce=nonce); c.update(salt)
    pt = c.decrypt(ct[:-16]); c.verify(ct[-16:]); return pt

s = open(f, "rb").read().decode().rstrip("\r\n")
d = gcm_dec(base64.b64decode(s[:3] + s[4:]), PW_OUTER)
cfg = json.loads(d)
for k in ["Payload","SNI","V2rayAddress","V2rayConfig","V2rayHost","V2raySNI"]:
    cfg[k] = gcm_dec(base64.b64decode(cfg[k]), PW_INNER).decode()
```

## Result (this file)
- Name: 🇧🇩 BD সার্কেল এবং রবি একদম আনলিমিটেড ফ্রি
- TunnelType: Http/Ws Proxy, Network: Websocket, Port 80 / 443
- CustomProxy: `13.224.245.41:80`, BugDNS 8.8.8.8, Route OVPN
- SNI `music.youtube.com`, V2rayAddress/Host `fcmtoken.googleapis.com` (Cloudfront)
- V2rayConfig: `vless://baf4383e-f585-4de7-99a9-b1733643bb59@telosbd.com:443
  ?path=/vless&security=tls&host=telosbd.com&type=ws&sni=telosbd.com
  #NurTsikenCubes~57.144.68.4~8080`
- Payload: standard ws-upgrade HTTP template
- Info: সকল জেলায় চলবে

## Files
- `/data/data/com.termux/files/usr/tmp/opencode/decoded_config.json`
- reverse artifacts: `/data/data/com.termux/files/usr/tmp/opencode/ultra/`

## Reusable decoder script
`/data/data/com.termux/files/usr/tmp/opencode/ultra_decode.py`
- `python3 ultra_decode.py file.ultra [out.json]`
- auto-detects the random char at base64 index 3, decodes both layers,
  falls back to plaintext for unencrypted fields
- verified: both copies of the BD/Robi config → identical JSON output

## Tool v1 — ZERONUT (2026-09-27 05:56)
- `ultra_tool.py` — decode/encode/scan/watch/verify/info/list + path shortcut
- `zeronut.py` — interactive menu (Encryption / Decryption / Scan / Watch / List / Info)
- `$PREFIX/bin/zeronut` — global shortcut (bash wrapper)
- Output: `/storage/emulated/0/Download/zeron------vai---pro/config<6digit>.json`
- Encode: same folder as input json, nam same + `.ultra`
- All in `/storage/emulated/0/Download/ultra-tunnel/` (+ `HOW_IT_WORKS.md` section 8)
