# Termux Emulated Storage (noExec FUSE) — Vite Build Fail

**Date**: 2026-09-30 23:25–23:50
**Device**: Android (Termux), project was at `/storage/emulated/0/Download/DevZeron`

## Problem
Vite/React project Termux e `/storage/emulated/0/...` e setup korte gele multiple fail:

1. `npm create vite` → spawn fail (code 127)
2. `npm install` → `EACCES: permission denied, symlink` (node_modules/.bin)
3. `npm install --no-bin-links` → esbuild postinstall fail: `spawnSync .../esbuild EACCES`
4. esbuild binary home e copy kore chalaleo → `ERR_DLOPEN_FAILED` (rollup native .node)
5. `esbuild-wasm` + `@rollup/wasm-node` alias → esbuild er **build API fs resolution totally fail** (transform kaj kore, file resolve kore na — kono fs e, home eo)
6. Native esbuild CLI home e kaj kore, kintu JS API (build) fail — **cwd emulated (FUSE noexec) e thakle service fs resolution fail**; cwd home e thakle kaj kore

## Root Cause
`/mnt/user/0/emulated` FUSE mount = `rw,nosuid,nodev,noexec`:
- symlink creation blocked (EACCES)
- binary exec blocked (noexec)
- Go binary (esbuild) fs stat syscall FUSE e fail → file resolution fail
- Node fs kaj kore (alada syscall path), tai npm install/read/write kaj kore
- Rollup native `.node` dlopen (mmap PROT_EXEC) noexec e fail
- Node 24 e `NODE_PATH` support nei
- Termux e `/usr/bin/env` nei → `#!/usr/bin/env node` shebang exec fail

## Solution (Working)
**Project files Termux home e rakho** (`/data/data/com.termux/files/home/DevZeron`), emulated storage e rakha jay na (source files er jonno).

Home e:
- Normal `npm install` (symlink + exec home fs e kaj kore)
- package.json scripts e vite ke node diye directly chalao:
  ```json
  "dev": "node node_modules/vite/bin/vite.js",
  "build": "node node_modules/vite/bin/vite.js build"
  ```
- esbuild binary home e → exec OK, rollup native home e → dlopen OK

## Lessons
- Termux e Vite project **kokhono** `/storage/emulated/0` e rakha jay na
- `npm create vite` Termux e spawn fail kore → manual scaffold koro
- esbuild-wasm diyeo kaaj kore na (build API fs resolution Termux e broken)
- Test command: `node node_modules/vite/bin/vite.js build`
