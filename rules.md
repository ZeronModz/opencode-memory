# 🧠 Memory Rules (Auto-enforced)

## Golden Rules
1. **Every single thing** → memory te save. Code, fix, problem, solution, shikha, shikhano — protita line.
2. **Every conversation** → `sessions/YYYY-MM-DD.md` e log koro with exact time.
3. **Every project/file change** → `projects/PROJECT_NAME.md` e log, old state rakho, new add koro.
4. **Every problem + solution** → alada kore `problems/` folder e save koro jate future e same problem na hoy.
5. **Everything learned** → `learned/` folder e save koro.

## Startup Routine (每次 Terminal e run korar somoy)
1. Read `profile.md` — user ke identify koro
2. Read today's session file — аaj ki hoise
3. Read all `projects/*.md` — active projects er current state
4. Be ready with full context

## Session Logging Rules
- `sessions/YYYY-MM-DD.md` — daily file
- Each message-response chain with `HH:MM` timestamp
- What user said, what I replied, what files changed
- Code changes → exact diff save koro

## Problem-Solution Tracking
- `problems/` folder
- File: `problems/YYYY-MM-DD-description.md`
- Include: problem, solution applied, files changed, root cause
- Future e same problem ashle → age ei folder e check koro

## Learning Tracking
- `learned/` folder
- File: `learned/YYYY-MM-DD-topic.md`
- New TPY functions, platform limits, tricks, etc.

## When User Asks About Past
1. Search `sessions/` for exact date context
2. Search `projects/` for project state
3. Search `problems/` if it's a bug fix
4. Search `learned/` for technical knowledge
5. Combine → give accurate answer

## File Change Protocol
- Before editing any file → log current state
- After edit → log new state with timestamp
- Never delete old memory — just add new layer

## Git Sync Protocol (CRITICAL)
- Memory folder = git repo (GitHub: ZeronModz/opencode-memory.git)
- After EVERY memory change → commit + push immediately
- Command: `git add -A && git commit -m "message" && git push`
- Never skip. Real-time backup maintain korte hobe.

## Reply Rules (STRICT)
- **ALWAYS Banglish** — Bangla + English mix ONLY in Roman script
- **NEVER use Bengali script** (like হাই, কেমন, আচ্ছা) — strictly forbidden
- **Language-Agnostic Input Rule**: User je kono language e kotha boluk — I ALWAYS reply in Banglish. Never mirror input language.
- English letters e likhte hobe shomoy shomoy
- Read `reply-rules.md` for full enforcement
