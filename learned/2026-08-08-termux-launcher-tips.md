# Learned: Termux boot/launcher best practices (2026-08-08)

## Termux shebang
- `/usr/bin` does NOT exist on Termux. Real path: `/data/data/com.termux/files/usr`.
- Correct shebang: `#!/data/data/com.termux/files/usr/bin/bash`.
- `#!/usr/bin/env bash`, `#!/bin/bash` → "bad interpreter" on direct exec.

## Root detection (robust order)
1. `id -u` == 0  (already root, e.g. booted into root shell / proot --root)
2. `sudo -n true`  (passwordless sudo, common after `fix-termux-root`)
3. `su -c 'id -u'` == 0
4. Scan known binaries: `/system/bin/su`, `/system/xbin/su`, `/sbin/su`, `/debug_ramdisk/su`, `/magisk/.core/bin/su`, `/su/bin/su`, `/su/xbin/su`, `/system/sbin/su`, `/system/product/bin/su`.
- On a real rooted Samsung (this phone): `/debug_ramdisk/su` exists and is executable → reliable marker.

## Auto-dependency check pattern
- `command -v binary` for each; collect missing; `pkg install -y "${missing[@]}"` (root-repo must be first for repos that need it).
- For repos/package-groups: use `pkg list-installed | grep -q "^name"`.
- Non-Termux fallback: `apt`. If neither → warn, never fail.

## Privilege escalation chain for Termux root tools
`sudo python3 ...` → else `tsu -c "python3 ..."` → else `su -c "python3 ..."` → else plain `python3`.
- `sudo -n true` as a gate, gives passwordless check.
- `tsu` is discontinued minus repo path on some devices; installing `sudo` from root-repo is the modern path (as `fix-termux-root` does).

## Spinner in bash
```bash
spin() { local msg="$1"; shift; "$@" >/tmp/log 2>&1 & local p=$!; local c=(⠋⠙⠹⠸⠼⠴⠦⠧⠇⠏) i=0
while kill -0 $p 2>/dev/null; do printf "\r%s %s " "$msg" "${c[$((i%10))]}"; i=$((i+1)); sleep 0.1; done
printf "\r\033[K"; wait $p; return $?; }
```

## Behavioral notes
- Bengali-script words (like "আপনার মোবাইলে ROOT নেই") inside bash `echo -e "..."` print fine on Termux (UTF-8 OK). Keep them for UX but echo English fallback too.
- In bootstrap `set -u` avoid when iterating possibly-empty arrays; guard or drop it.