# 2026-08-15 — .so Reverse Engineering in Termux (Android/ARM64)

## Context
`/storage/emulated/0/MT2/apks/` er duita stripped ARM aarch64 .so decompile kora (MT Manager recycle).

## Learned — Key Techniques

### 1. JNI_OnLoad full decode (no Ghidra needed)
- `llvm-nm -D --defined-only` → exported funcs (often only JNI_OnLoad for Android JNI libs)
- JNIEnv offset table (AArch64, 64-bit): 
  - **FindClass** = +0x30 (`[env+0x30]`)
  - **GetStaticFieldID** = +0x2F0
  - **RegisterNatives** = +0x6B8
  - **DeleteLocalRef** = +0xB8
  - **CallIntMethodV** = +0x6A8
  - **NewByteArray** = +0x5E8, **SetByteArrayRegion** = +0x558, **GetByteArrayRegion** = +0x628
  - **GetDirectBufferAddress** = +0x328
- Disasm e kon offset e `blr` dekhte pavo → ota kun JNI call.

### 2. RegisterNatives table vana .data.rel.ro e ZERO dekhabe!
- .data.rel.ro pointer tables relocation-based → llvm-objdump -s e shob 0.
- JNINativeMethod = 3 pointers (name*, sig*, fnptr*) → **`llvm-readelf -r` er R_AARCH64_RELATIVE** theke resolve korte hobe.
- Table entries order: `name addr, sig addr, fn addr` (8 bytes each).

### 3. Android system property check (SDK version in native)
- `__system_property_get("ro.build.version.sdk")` + `atoi` → runs version branch.
- Compiled-in `getProperty` via PLT `__system_property_get@plt`. Atoi PLT catch korte gelei bujhben SDK detect hocche.

### 4. Name that stripped translation unit
- `strings` er `../../../path/...` prefix → return-path of compiler (e.g. belle-sip, liblinphone) → identify open-source project → compare with upstream.

### 5. Useful binutils-on-Termux support
- `readelf`, `objdump`, `nm`, `strings`, `llvm-objdump`, `llvm-readelf`, `llvm-nm` all present in Termux by default.
- Ghidra / radare2 NOT installed; clang/lld versions = NDK r25c/r27 detected from .comment.

### 6. File storage guidance
- 28MB .so te `strings | grep` long-chain korle output matha kharap — sempre pipeline + sort -u + head.
- Orphan nirdisto focused architecture: first `file`, then `readelf -h`, then fast grep, then slow full disasm last.

## Identified Libraries (fingerprint → project)
- `JNI` e `Java_org_linphone_core_*` → Linphone Android SDK (org.linphone.core package)
- `xmlXPath*`, `belle_*`, `ms*`, `ortp_*`, `mbedtls*`, `sqlite*`, `srtp*`, `speex*`, `opus*` → statically bundled open source.