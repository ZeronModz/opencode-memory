# 📚 Learned (2026-09-27): Go binary RE in Termux — pclntab + AndroidLibXrayLite crypto

## 1. Go `pcHeader` layout (Go 1.18 … 1.27) — the trap
```
offset size  field
0      4     magic   0xFFFFFFF1
4      1     pad1 = 0
5      1     pad2 = 0
6      1     minLC   (4 on arm64)
7      1     ptrSize (8)
8      8     nfunc   (int)
16     8     nfiles  (uint)
24     8     _ uintptr        <-- UNUSED "textStart" field (STILL PRESENT!)
32     8     funcnameOffset
40     8     cuOffset
48     8     filetabOffset
56     8     pctabOffset
64     8     pclnOffset
```
Missing the `_ uintptr` at offset 24 shifts EVERYTHING → garbage parse.
(Comment in `src/runtime/symtab.go`: "This unused field can be removed in
coordination with Delve" — so tools must keep handling it.)

## 2. textStart ≠ section start
- `.text` VMA = `0x527630`, but real Go funcs begin at `0x527680` (0x50 preamble).
- `func_addr = textStart + entryoff` from `functab`.
- `functab` @ `PT + pclnOffset` = `nfunc+1` pairs of `u32 entryoff, u32 funcoff`.
- `_func` @ `PT + pclnOffset + funcoff` = `{u32 entryoff, i32 nameoff, ...}`
- name string = `PT + funcnameOffset + nameoff`, NUL-terminated.
- Sanity check: prologue pattern `ldr x16,[x28,#0x10]; sub x17,sp,#N; cmp; b.ls`
  = Go stack-growth check at every function entry.
- Go arm64 call targets = exact function entry (no mid-function branches),
  except the `morestack` stub placed AFTER the body which jumps back to entry.

## 3. Tools status on Termux
- `go tool nm / objdump / addr2line` → **all fail** on gomobile `.so`
  ("no symbol section" / returns `?`). Must parse pclntab manually.
- `llvm-objdump -d --no-show-raw-insn` works (2.9M lines here) but prints
  useless `<crosscall2+...>` labels — resolve names yourself from pclntab.
- `nm -D` only shows JNI/C-exported symbols, not Go symbols.
- No Ghidra / radare2 / capstone / root on this phone.

## 4. Go arm64 register ABI — how to read argon2 params
`argon2.deriveKey(mode int, password, salt, secret, data []byte,
time, memory uint32, threads uint8, keyLen uint32) []byte`
= 17 params → 16 regs + 1 stack arg.
```
x0        mode (2 = argon2id)
x1..x3    password (ptr,len,cap)
x4..x6    salt
x7..x9    secret = nil → 0,0,0
x10..x12  data   = nil → 0,0,0
w13       time
w14       memory
w15 (u8)  threads
[sp+8]    keyLen (caller), read at [sp+0x108] (callee)
```
Callee `deriveKey` body proves it: `cbz w13 → panic` (time<1),
`ubfx x16,x15,#0,#8; cbz w16 → panic` (threads<1), `udiv ... [sp+0x17c]` = memory.

## 5. Interface (2-word) returns in Go ABI
`cipher.NewGCM(block) (AEAD, error)` → x0 = **itab ptr**, x1 = data ptr,
x2/x3 = error iface. Method `Open` = itab method slot 2 → `ldr x13,[x0,#0x20]`
(itab layout: `inter`(8) + `_type`(8) + `hash`(4) + pad(4) = 24 = 0x18 → fun[0]=Seal,
fun[1]=Open @0x20).

## 6. Ultra Tunnel / AndroidLibXrayLite crypto facts
- Outer pw `"/4ssU0OjjtKH8AheiAxnc3DwSGDzsy+8"` (32B), inner pw `"NurChickenPowder"` (16B)
- Both: argon2id(3, 8192 KiB, 1, 32) + AES-256-GCM, blob = salt|nonce|ct, **AAD = salt**
- Outer adds a **single random char at base64 index 3** (`concatstring3`);
  inner (`EO`) does not.
- `crypto/rand.Reader` global @ `0x1d60070/78` — used for salt + nonce generation.

## 7. GCM AAD gotcha (Python)
`pycryptodome` here has no `mac_data=` kwarg → use `c.update(aad)` BEFORE
`c.decrypt(...)`, then `c.verify(tag)`.
