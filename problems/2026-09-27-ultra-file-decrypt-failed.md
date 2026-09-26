# ⚠️ Problem (2026-09-27): `.ultra` decrypt kept failing

## Problem
Decrypting `🇧🇩 BD সার্কেল এবং রবি একদম আনলিমিটেড ফ্রি.ultra` kept failing —
Argon2+GCM tag never verified, across thousands of brute-force combos.

## Root causes (4 stacked bugs)
1. **Wrong base64 stripping** — tried dropping first/last chars. Real format:
   one random char is inserted at **index 3** by `concatstring3`
   (`b64[:3] + randomLetter + b64[3:]`). Correct = `s[:3] + s[4:]`.
   (2017 = 2016 b64 + 1 inserted char.)
2. **AAD not passed** — `decrypt` passes `data = blob[0:16]` (= salt) as GCM
   additional authenticated data. Without AAD the tag check always fails.
3. **Wrong Argon2 params** — time/memory/threads are in **x13/x14/x15**, not
   x7/x8/x9 (because `deriveKey` also takes `secret` and `data` []byte).
   Real: `t=3, m=8192, p=1, keyLen=32` (keyLen is the 17th arg, on stack).
4. **Wrong password theory** — believed a runtime global at `0x1d60070`
   held the password. It is actually `crypto/rand.Reader` (set by
   `crypto/rand.init.0`). Password is a compile-time constant copied in code.

## Secondary blocker
Go pclntab parse gave garbage → no symbol names → wrong call analysis.
Fixed by handling the unused `_ uintptr` (textStart) field at pcHeader+24
and using `textStart = 0x527680` (not `.text` VMA `0x527630`).

## Solution applied
Manual pclntab parser (`ultra/ents.pkl`) + full disassembly `ultra/full.asm`,
then read `decrypt`/`Encrypt`/`EO`/`deriveKey` bodies to extract exact params.

## Files changed
- created `/data/data/com.termux/files/usr/tmp/opencode/ultra/{full.asm,ents.pkl,EO.asm,DO.asm,GV.asm}`
- created `/data/data/com.termux/files/usr/tmp/opencode/decoded_config.json`

## Verified
GCM tag verified + plaintext is valid JSON with readable values → correct.

## Future rule
Any `AndroidLibXrayLite`-based `.ultra`/Ultra Tunnel file →
check `projects/ultra-tunnel-ultra-decode.md` first, do NOT re-brute-force.
