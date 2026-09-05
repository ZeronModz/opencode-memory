# Project: MonerKothaBot Telegram Mini App

## Status: Active
## File: `/storage/emulated/0/htdocs/index.html`

## Description
Telegram Mini App (WebApp) for MonerKothaBot - a user-friendly interface to access all bot tools without needing to use Telegram commands.

## Architecture
- Single Page Application (SPA) with view-based navigation
- Views: Home → Category → Detail
- No external JS frameworks - pure vanilla JS
- CSS animations and transitions
- Telegram WebApp API integration

## Features
- 4 tool categories with 14+ tools
- Name input → link generation for card tools
- Copy to clipboard
- Static link buttons for direct tools
- Haptic feedback (Telegram)
- Back button support (Telegram)
- Responsive design
- Safe area inset support
- Floating orb background effects
- Staggered entry animations

## Design System
- Colors: Dark ink (#0f0a12), Rose (#e85d8a), Gold (#dbb35a), Purple (#9b6dff), Cyan (#4dd9c0)
- Fonts: Baloo Da 2 (headings), Hind Siliguri (body)
- Border radius: 12-16px
- Card style: Glass-morphism with subtle borders

## Categories & Tools
### Love Webs
- Birthday Wish 1.0, 2.0, Premium
- Anniversary A1
- Love Certificate
- Love You More
- Will You Be My Gurl?
- Do You Like Me?
- Flower Propose 1.0
- Memory Game with Crash

### Prank Webs
- Horror Sound Prank
- 18+ Sound Prank

### Other Tools
- How Many S-Line
- Camera Hack
- MetraX (2 versions)

### Bomber
- Zeron Bomber

## URL Deep Linking
- `?tool=tool-id` parameter opens specific tool directly
