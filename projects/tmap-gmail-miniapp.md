# Tmap Gmail Mini App

## Status: Active (v3 — Redesigned)

## Last Updated: 2026-07-28 23:50

## Description
Telegram Mini App for Tmap Gmail bot. Clean modern flat design (light theme). Users view Gmail accounts, read inbox, generate emails, manage settings — inside Telegram.

## Tech Stack
- Single HTML file (inline CSS + JS, zero external dependencies)
- Telegram WebApp SDK v6+ (CDN)
- Vercel serverless function: `api/proxy.mjs`
- Backend API: `https://zeron-gmail.vercel.app/api/`

## Files
- `index.html` — All HTML, CSS, JS inline (single file)
- `api/proxy.mjs` — Vercel serverless, reads APP_PASS from env
- `vercel.json` — Static + Node deployment + CSP

## Design System (v3 — Clean Modern Flat)
- **Background:** Slate-50 (#F1F5F9)
- **Cards:** White (#FFFFFF) with subtle shadow
- **Primary:** Teal (#0D9488 / #14B8A6)
- **Text:** Slate-900 (#0F172A) / Slate-500 (#64748B)
- **Accent:** Orange (#F97316) for CTAs
- **Destructive:** Red (#EF4444)
- **Border Radius:** 12px cards, 8px small elements
- **Typography:** System sans-serif (-apple-system, Inter, Segoe UI)
- **Transitions:** 150-200ms eased-in-out
- **Shadows:** Subtle multi-layer (0 1px 3px rgba(0,0,0,0.06))
- **Nav:** Card-style white bottom bar with teal top indicator

## Features
- Bottom nav: Profile, Inbox, Generate, Settings
- Telegram user profile with premium badge
- Account list with detail/inbox/copy/delete
- Inbox with pagination + search
- Generate: Dot Trick, Plus Alias, Mixed, Custom
- API proxy via Vercel env var (APP_PASS)
- Export, Tutorial, Developer info
- Modal, Toast notification, Empty states

## Vercel Env
- `APP_PASS` — matches bot's api_pass (/zeron-pass)

## Design Evolution
- v1: Brutalism dark (ES modules — failed in WebView)
- v2: Brutalism dark (single file, stronger)
- v3: Clean modern flat light theme (current)
