# Project: ZerFi (github.com/ZeronModz/ZerFi)

**Owner:** Hasan (DevZeron) · **Platform:** Android/Termux · **Purpose:** WPS (Wi-Fi Protected Setup) security auditing tool (Pixie Dust / Bruteforce) on Termux.

## Before this session (old state)
- v2.0: full rewrite, global `zerfi` command, `main.py` engine obfuscated, `w1.py` legacy engine, session mgmt, HTML reports, `help.py` interactive guide, `contact.py`.
- Installer: `installer.sh` / `install.sh`, `fix.sh` root repair, website on Vercel (public/).
- Note: `main.py` is obfuscated (7849 KB packed payload ~7.8K lines) — edits go in the helper shell scripts + website, never in the engine payload.
- `zerfi update` was DISABLED ("DevZeron Edition").

## v3.0 — what changed (2026-08-08)
| File | Change |
|---|---|
| `bootstrap.sh` (NEW) | Auto-install missing Termux modules + auto-repair + ROOT gate (Bengali "no root" message). Modes: run/check/root/install. |
| `install.sh`, `installer.sh` | Rewritten launcher: `zerfi check`, `zerfi root`, working `zerfi update`, auto-bootstrap on launch, sudo→tsu→su fallback, absolute bash shebang. |
| `fix.sh` | `preflight_root()` + `no_root_message()` early gate. |
| `help.py` | Documents new commands in menu 9 + intro bullets. |
| `README.md` | v3.0, new commands table, ROOT-Gate bullets, troubleshooting rows. |
| `CHANGELOG.md` | v3.0 entry (Auto-Install, Auto-Repair, ROOT Gate, update, new commands). |
| `public/index.html` | v3.0 title/badges, 3 new feature cards, ROOT required banner + Bengali message box. |
| `public/style.css` | badge-c, root-banner pulse/blink/glow animations, stagger delays 7-22. |

## Key design decisions
- **Root gate runs in run mode only** — `help`/`contact`/`fix`/`update`/`check`/`root` work even on a non-rooted phone; only actually launching an attack is blocked.
- **Auto-install never blocks** — if `pkg/apt` missing or install fails, warn only (doesn't crash).
- `zerfi` bin shebang MUST be `#!/data/data/com.termux/files/usr/bin/bash` — stable path on Termux.
- Engine `main.py` untouched (obfuscated); all features added at launcher/bootstrap layer.

## Files (current v3.0)
```
ZerFi/
├── main.py          # obfuscated engine (userchanged)
├── w1.py            # legacy engine (obfuscated)
├── bootstrap.sh     # NEW v3.0 auto-install/repair/root-gate
├── install.sh        # local setup + launcher writer
├── installer.sh      # one-command fresh install
├── fix.sh           # root repair (now with early root gate)
├── help.py          # interactive help
├── contact.py        # dev contact menu
├── README.md / CHANGELOG.md
└── public/          # Vercel website (index.html, style.css, script.js, banner.svg)
```

## Install command
```
curl -sLo installer.sh https://raw.githubusercontent.com/ZeronModz/ZerFi/main/installer.sh && bash installer.sh
```

## Status
- Working tree has v3.0 changes staged — **NOT committed/pushed yet** (waiting for user's go-ahead).