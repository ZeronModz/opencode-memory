# UI/UX Pro Max — Complete Skill Knowledge

## Date: 2026-07-29

## Overview
- **What it is:** Comprehensive design guide for web and mobile applications
- **Location:** `/data/data/com.termux/files/home/.opencode/skills/ui-ux-pro-max/`
- **Base dir:** `/data/data/com.termux/files/home/.opencode/skills/ui-ux-pro-max/`
- **Contains 67 styles, 96 color palettes, 57 font pairings, 99 UX guidelines, and 25 chart types across 13 technology stacks**
- **Searchable database with priority-based recommendations**

## Data Files
| File | Rows | Description |
|------|------|-------------|
| `data/styles.csv` | 68 | 67 UI styles (minimalism, neumorphism, glassmorphism, brutalism, 3D, etc.) |
| `data/products.csv` | 97 | Product type recommendations |
| `data/colors.csv` | 97 | Color palettes by product type |
| `data/typography.csv` | 58 | Font pairings and typography |
| `data/landing.csv` | 28 | Landing page structures and CTA strategies |
| `data/charts.csv` | 26 | Chart types and library recommendations |
| `data/ux-guidelines.csv` | 100 | UX best practices and anti-patterns |
| `data/icons.csv` | 101 | Icon guidelines and sets |
| `data/web-interface.csv` | 31 | Web interface guidelines |
| `data/react-performance.csv` | 45 | React/Next.js performance patterns |

## Stacks (13 total)
| Stack | Focus |
|-------|-------|
| html-tailwind | Tailwind utilities, responsive, a11y (DEFAULT) |
| react | State, hooks, performance, patterns |
| nextjs | SSR, routing, images, API routes |
| vue | Composition API, Pinia, Vue Router |
| svelte | Runes, stores, SvelteKit |
| swiftui | Views, State, Navigation, Animation |
| react-native | Components, Navigation, Lists |
| flutter | Widgets, State, Layout, Theming |
| shadcn | shadcn/ui components, theming, forms, patterns |
| jetpack-compose | Composables, Modifiers, State Hoisting, Recomposition |
| astro | Static site generation |
| nuxtjs | Nuxt 2 |
| nuxt-ui | Nuxt UI components |

## Search Domains
| Domain | Use For |
|--------|---------|
| product | Product type recommendations |
| style | UI styles, colors, effects |
| typography | Font pairings, Google Fonts |
| color | Color palettes by product type |
| landing | Page structure, CTA strategies |
| chart | Chart types, library recommendations |
| ux | Best practices, anti-patterns |
| react | React/Next.js performance |
| web | Web interface guidelines |
| prompt | AI prompts, CSS keywords |

## Scripts
- `scripts/search.py` — Main search tool
- `scripts/design_system.py` — Design system generation
- `scripts/core.py` — Core logic

## Workflow
1. Analyze requirements (product type, industry, style, stack)
2. Generate design system: `python3 scripts/search.py "<query>" --design-system`
3. Persist design system with `--persist` flag (creates design-system/MASTER.md + pages/)
4. Supplement with detailed searches per domain
5. Get stack guidelines with `--stack <stack>`

## Output Formats
- ASCII box (default) — terminal display
- Markdown (`-f markdown`) — documentation

## Key Rules for Professional UI
- No emoji as icons — use SVG (Heroicons, Lucide)
- Stable hover states — color/opacity transitions, not scale transforms
- Correct brand logos — use Simple Icons SVG
- Consistent icon sizing — fixed viewBox 24x24 with w-6 h-6
- Cursor pointer on all clickable elements
- Smooth transitions 150-300ms
- Light mode text: `#0F172A` (slate-900), muted: `#475569` (slate-600)
- Floating navbar: `top-4 left-4 right-4`
- Consistent max-width: `max-w-6xl` or `max-w-7xl`
- Responsive at 375px, 768px, 1024px, 1440px
- WCAG AA 4.5:1 minimum contrast