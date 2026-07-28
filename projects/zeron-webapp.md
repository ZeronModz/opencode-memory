# Project: Zeron WebApp (Mail Viewer)

**Path:** `/storage/emulated/0/htdocs/zeron-webapp/index.html`
**Type:** Static HTML/CSS/JS — Temp Gmail Message Viewer

---

## Latest State (2026-07-28 — FINAL)

### Concept
Modern app interface inspired by 21st.dev/shadcn design patterns.
Original warm vintage colors applied to a clean, modern layout.

### Design Tokens
```
--violet: #8a76c9
--violet-deep: #5f4baa
--pink: #f0a8c9
--gold: #eeb949
--sage: #9cb68f
--sky: #8db3ea
--bg-1: #f6f1fb (soft lavender)
--bg-2: #fdf2f6 (soft pink)
--card-bg: #fffcf9 (warm cream)
--ink: #322b52 (deep purple)
--radius-xl: 32px
```

### Typography
- **Heading:** Playfair Display (700, italic accent)
- **UI/body:** Inter (clean sans-serif)

### Structure
- Masthead: badge + h1 + subtitle + divider dots
- Loading: pulsing ring loader + skeleton card + blink label
- Content: card with gradient accent top bar → sender avatar/row → field grid (To/Date/Subject/UID) → tag chips → message body → footer
- Error: centered card with icon

### Special Features
- Gradient accent bar (violet→pink→gold→sage→sky) on card top
- Animated typing effect for message body
- Dynamic date tag (today/yesterday/date)
- URL param based rendering (`?from=&to=&date=&subject=&body=&uid=`)
- Context menu, copy, select, drag prevention

---

## Design History

| Iteration | Concept | Status |
|-----------|---------|--------|
| Original | Vintage letter theme (Fraunces, Special Elite, tear-edge, sparkles, postmark) | Replaced |
| V1 | Light glassmorphism (indigo/amber, Inter+Playfair, glass card, skeleton) | Replaced |
| V2 | Dark cosmic 3D (space bg, perspective card, holographic seal) | REJECTED |
| V3 (current) | Modern 21st.dev-inspired (original colors, clean card, tags, gradient accent) | ✅ FINAL |

### What stayed same across all versions
- Same JS logic (getParam, decodeSafe, typeBody, getInitials)
- Same HTML IDs for DOM references
- Same URL parameter handling
- Same no-select/no-copy/no-contextmenu guards
- Same prefers-reduced-motion support
