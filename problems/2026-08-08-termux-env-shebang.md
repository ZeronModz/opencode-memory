# Problem: `/usr/bin/env` missing on Termux → "bad interpreter" (2026-08-08)

## Symptom
After rewriting the `zerfi` launcher with shebang `#!/usr/bin/env bash`, running it failed:
```
/data/data/com.termux/files/usr/bin/zerfi: bad interpreter: /usr/bin/env: no such file or directory
```

## Root cause
Termux has **no `/usr/bin/` directory at all** — `$PREFIX` is `/data/data/com.termux/files/usr`, so `env`, `bash` etc. live at `/data/data/com.termux/files/usr/bin/*`. `/usr/bin/env` doesn't exist, so the kernel can't even start the script.

## Fix (applied)
Use the absolute Termux path for the shebang in ALL launchers/scripts:
```
#!/data/data/com.termux/files/usr/bin/bash
```
Changed in: `bootstrap.sh`, and the launcher heredocs written by `install.sh` + `installer.sh`.

## Prevention rule (learned)
- Termux scripts: ALWAYS use `#!/data/data/com.termux/files/usr/bin/bash`, never `#!/usr/bin/env bash` or `#!/bin/bash`.
- If invoking a script with `bash script.sh`, shebang is ignored (worked even when wrong), but direct `./script` or `exec` breaks.
- Bare `fix.sh` etc. must keep `bash fix.sh` style invocation OR update the shebang.