# DevZeron Portfolio Website

**Started**: 2026-09-30 23:17
**Status**: Phase 1 COMPLETE (Preloader + Navigation + Hero)
**Location**: `/data/data/com.termux/files/home/DevZeron` (Termux home — emulated storage noexec, see problems/2026-09-30-termux-emulated-noexec-vite.md)
**Original asset path**: `/storage/emulated/0/Download/DevZeron/images/hero1.png` (copied to `public/images/hero1.png`)

## Tech Stack
- React 18 + Vite 5.4
- GSAP 3.12 (entrance + parallax)
- Three.js 0.169 (installed, Phase 2 er jonno — `src/components/three/ThreeCanvas.jsx` skeleton)
- Fonts: Anton (display), Space Grotesk (body), JetBrains Mono (labels) — Google Fonts + fallback stacks

## Structure
```
DevZeron/
  index.html
  package.json
  public/images/hero1.png
  src/
    main.jsx
    App.jsx
    components/
      preloader/Preloader.jsx   — wordmark + progress bar + pct, GSAP timeline, ~2.2s
      navigation/Navigation.jsx — fixed, mix-blend-difference, mobile burger + full-screen menu
      hero/Hero.jsx             — bg typography, portrait, intro, deco, parallax, entrance
      three/ThreeCanvas.jsx     — Phase 2 skeleton (renders null)
      layout/                   — empty (future)
    sections/                   — empty (future)
    hooks/usePrefersReducedMotion.js
    styles/global.css           — full design system
```

## Design
- bg #050505, fg #f2f2f0
- Hero: 100dvh, outlined "DEVZERON" (Anton, clamp 24vw) behind portrait
- Portrait: right side desktop (92vh, bottom fade mask), centered mobile (43vh)
- Film grain overlay (SVG noise, opacity 0.05)
- Intro bottom-left desktop / centered mobile
- Parallax: pointer fine only, bg 5px / portrait 10px / intro 3px
- Reduced motion: hook + CSS fallback

## Commands
```bash
cd ~/DevZeron
npm run dev      # node node_modules/vite/bin/vite.js
npm run build
```

## Verified
- `npm run build` ✓ (38 modules, 4.93s)
- dev server ✓ http://localhost:5173 (HTML + hero1.png 200, 1080180 bytes)

## Next: Phase 2
- About section
- Three.js/WebGL hero layer (ThreeCanvas)
- Scroll animations
- Then: Skills, Projects, Contact, Footer
