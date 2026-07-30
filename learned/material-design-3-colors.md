# Material Design 3 — Color System Complete Reference

## Source: m3.material.io + coloracci.ai guide

## Overview
M3 (Material You) dynamically generates color schemes from a single source color — even from the user's wallpaper.

## Tonal Palette System
Each palette has 13 tones: 0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 95, 99, 100

### M3 Default Purple (#6750A4) as Reference
| Tone | Value | Usage |
|------|-------|-------|
| 0 | #000000 | — |
| 10 | #21005D | Primary dark container |
| 20 | #381E72 | — |
| 30 | #4F378B | — |
| 40 | #6750A4 | Primary (light theme) |
| 50 | #7F67BE | — |
| 60 | #9A82DB | — |
| 70 | #B69DF8 | — |
| 80 | #D0BCFF | Primary (dark theme) |
| 90 | #EADDFF | Primary Container (light) |
| 95 | #F6EDFF | — |
| 99 | #FFFBFE | Background/Surface |
| 100 | #FFFFFF | — |

## Color Roles
### Primary
- **Primary**: #6750A4 — FABs, prominent buttons, active states
- **On Primary**: #FFFFFF — text/icons on primary
- **Primary Container**: #EADDFF — chips, cards (less prominent)
- **On Primary Container**: #21005D — text on primary container

### Secondary
- **Secondary**: #625B71 — filter chips, less prominent actions
- **Secondary Container**: #E8DEF8 — navigation bars, tonal buttons

### Tertiary
- **Tertiary**: #7D5260 — accents to balance primary/secondary
- **Tertiary Container**: #FFD8E4 — complementary accent areas

### Error
- **Error**: #B3261E — error states, destructive actions
- **Error Container**: #F9DEDC — error background

### Surface
- **Surface**: #FFFBFE — page backgrounds, cards
- **Surface Variant**: #E7E0EC — differentiated surfaces
- **On Surface**: #1C1B1F — primary text
- **On Surface Variant**: #49454F — secondary text, icons

### Outline
- **Outline**: #79747E — borders, dividers at emphasis
- **Outline Variant**: #CAC4D0 — lower-emphasis borders

## M3 vs M2 Changes
| Feature | M2 | M3 |
|---------|-----|-----|
| Color source | Fixed palette | Dynamic/seed-based |
| Palette size | Primary + Accent | Primary + Secondary + Tertiary |
| Shading | 100-900 | Tonal 0-100 |
| Dark mode | Manual inversion | Automatic tone shifting |
| Personalization | None | Wallpaper-based |
| Accessibility | Manual checking | Built-in contrast |
| Container | Not explicit | Primary/Secondary/Tertiary containers |

## CSS Implementation (Web)
```css
:root {
  --md-sys-color-primary: #6750A4;
  --md-sys-color-on-primary: #FFFFFF;
  --md-sys-color-primary-container: #EADDFF;
  --md-sys-color-surface: #FFFBFE;
  --md-sys-color-on-surface: #1C1B1F;
  --md-sys-color-outline: #79747E;
}

@media (prefers-color-scheme: dark) {
  :root {
    --md-sys-color-primary: #D0BCFF;
    --md-sys-color-on-primary: #381E72;
    --md-sys-color-primary-container: #4F378B;
    --md-sys-color-surface: #1C1B1F;
    --md-sys-color-on-surface: #E6E1E5;
    --md-sys-color-outline: #938F99;
  }
}
```

## Kotlin/Compose Implementation
```kotlin
val lightColors = lightColorScheme(
  primary = Color(0xFF6750A4),
  secondary = Color(0xFF625B71),
  tertiary = Color(0xFF7D5260)
)

val darkColors = darkColorScheme(
  primary = Color(0xFFD0BCFF),
  secondary = Color(0xFFCCC2DC),
  tertiary = Color(0xFFEFB8C8)
)
```

## Design Token Architecture
- **ref** (reference): Raw values (--md-ref-palette-*, --md-ref-typeface-*)
- **sys** (system): Role-based (--md-sys-color-*, --md-sys-typescale-*)
- **comp** (component): Element-specific (--md-filled-button-*, etc.)

## Common Color Combinations for App Building
### Light Theme (default)
- Primary: #6750A4
- On Primary: #FFFFFF
- Primary Container: #EADDFF
- On Primary Container: #21005D
- Secondary: #625B71
- Surface: #FFFBFE
- On Surface: #1C1B1F
- Surface Variant: #E7E0EC
- Outline: #79747E

### Dark Theme
- Primary: #D0BCFF
- On Primary: #381E72
- Primary Container: #4F378B
- On Primary Container: #EADDFF
- Secondary: #CCC2DC
- Surface: #1C1B1F
- On Surface: #E6E1E5
- Surface Variant: #49454F
- Outline: #938F99
