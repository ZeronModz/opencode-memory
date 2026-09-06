# MonerKothaBot Mini App Project

## Created: 2026-09-06
## Location: /storage/emulated/0/htdocs/

## Files
- `index.html` - Main entry point (483 lines)
- `style.css` - Beautiful modern design (1155 lines)
- `app.js` - Telegram Mini App logic (615 lines)

## Features
1. **Splash Screen** - Animated logo with loading bar
2. **Subscribe Gate** - Channel join + verify (CodeDevZeron, MonerKothaa)
3. **Home Page** - Profile card, stats, quick actions, about, developer info
4. **Tools Page** - Categorized tools with search
   - Love Webs (10 tools): Birthday v1/v2/premium, Anniversary, Memory Game, Do You Like Me, Flower Propose, Love You More, Will You Be My Gurl, Love Certificate
   - Prank Webs (2 tools): Horror Prank, 18+ Sound Prank
   - Others (3 tools): S-Line, Zeron Bomber, MetraX
5. **Profile Page** - Full Telegram user info + settings
6. **Bottom Navigation** - Home, Tools, Profile with active indicators

## Telegram APIs Used
- `WebApp.expand()` - Auto expand
- `WebApp.requestFullscreen()` - Fullscreen toggle
- `WebApp.BackButton` - Back navigation
- `WebApp.MainButton` - Bottom button
- `WebApp.HapticFeedback` - Touch feedback
- `WebApp.CloudStorage` - Local storage
- `WebApp.setHeaderColor()` - Theme sync
- `WebApp.setBackgroundColor()` - Theme sync
- `WebApp.setBottomBarColor()` - Theme sync
- `WebApp.onEvent('themeChanged')` - Live theme
- `WebApp.enableClosingConfirmation()` - Close confirm
- `WebApp.addToHomeScreen()` - Home shortcut
- `WebApp.initDataUnsafe.user` - User data
- `getChatMember` API - Channel subscription check

## Design
- Dark/Light theme auto-sync with Telegram
- CSS variables for all colors
- Safe area insets respected
- Modern card-based UI
- Smooth animations
- Mobile-first responsive

## TODO
- [ ] Replace `YOUR_BOT_TOKEN_HERE` with real bot token in app.js
- [ ] Or better: Create a backend proxy for channel check
- [ ] Test in Telegram client
- [ ] Deploy to hosting (Netlify/Vercel/etc)

## Deploy (2026-09-06)
- **Vercel URL**: https://monerkotha-bot.vercel.app
- **Vercel Dashboard**: https://vercel.com/dev-zeron/monerkotha-bot/XeqiJq9jc3LgkbWdFfojDuCfHybj
- Token used for deploy

## Fix Deploy (2026-09-06 2nd)
- Fixed: Channel join buttons now use `tg.openTelegramLink()`
- Fixed: Bot token updated
- Fixed: Channel IDs corrected (CodeDevZeron: -1002296425479, MonerKothaa: -1002563935487)
- Fixed: All buttons now work properly
- URL: https://monerkotha-bot.vercel.app
