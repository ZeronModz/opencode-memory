# 2026-08-08 skillfish CLI fix on Termux (Android)

## Problem
- `npx skillfish add <repo> <skill>` → `sh: 1: skillfish: not found`
- After `npm i -g skillfish`: `skillfish` ran but
  - `#!/usr/bin/env node` → zsh: bad interpreter: `/usr/bin/env: no such file or directory` (Termux e /usr/bin/env nei)
  - After fixing shebang: `ERR_MODULE_NOT_FOUND: Cannot find package 'commander'` — because npm placed a COPY of `dist/index.js` at `/usr/bin/skillfish`, import path wrong.

## Root Cause
Termux has no `/usr/bin/env` AND global bin file gets physically copied, breaking ESM `import` resolution (commander resolved from wrong base).

## Fix (use on any new phone)

1. `npm i -g skillfish`
2. Create wrapper:
```bash
cat > /data/data/com.termux/files/usr/lib/node_modules/skillfish/bin/skillfish-shim.sh <<'EOF'
#!/data/data/com.termux/files/usr/bin/bash
exec node /data/data/com.termux/files/usr/lib/node_modules/skillfish/dist/index.js "$@"
EOF
chmod +x .../skillfish-shim.sh
```
3. Replace bin:
```bash
mv /usr/bin/skillfish /usr/bin/skillfish.orig
ln -s /data/data/com.termux/files/usr/lib/node_modules/skillfish/bin/skillfish-shim.sh /usr/bin/skillfish
```
4. Verify: `skillfish --version` → 1.0.39; `skillfish --help` exit 0.

## Note

- Use `skillfish` directly, NOT `npx skillfish`.
- Skill installs to `~/.opencode/skills/<name>` (OpenCode agent). Tip: run with `node $(npm root -g)/skillfish/dist/index.js add ...` works even before wrapper fix.
- Session date 2026-08-08, device: Android/Termux, zsh.