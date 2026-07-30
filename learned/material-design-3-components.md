# Material Design 3 — Components Reference

## Source: material-web.dev + m3.material.io

## Available M3 Components
- Buttons (Filled, Outlined, Text, Elevated, Floating Action Button)
- Checkbox
- Chips (Filter, Input)
- Dialogs (Alert, Date/Time Picker)
- FAB (Floating Action Button)
- Icon Buttons
- Lists
- Menus
- Progress Indicators (Linear, Circular)
- Radio Buttons
- Ripple
- Select (Dropdown Menu)
- Sliders
- Switch (Toggle)
- Tabs
- Text Field (Outlined, Filled, Standard)

## Design Token Hierarchy
Component tokens → System tokens → Reference tokens → Concrete values

### Token Prefixes
- `md.ref.*` — Reference tokens (raw values like hex colors, font sizes)
- `md.sys.*` — System tokens (role-based mappings for theming)
- `md.comp.*` — Component tokens (specific UI element overrides, no prefix in CSS)

### System Token Categories
| Category | Prefix | Examples |
|----------|--------|----------|
| Color | md-sys-color | primary, on-primary, primary-container |
| Typography | md-sys-typescale | body-medium, headline-large |
| Shape | md-sys-shape | corner-small (4px), corner-medium (6px), corner-large (8px) |
| Elevation | md-sys-elevation | 0, 1, 3, 6, 8, 12dp |

### Shape Tokens
- Corner Small: 4px
- Corner Medium: 6px
- Corner Large: 8px
- Corner Extra Large: 16px
- Corner Full: 9999px (pill/circle)

### Typescale Tokens (Type Roles)
- Display Large/Medium/Small
- Headline Large/Medium/Small
- Title Large/Medium/Small
- Body Large/Medium/Small
- Label Large/Medium/Small

### Elevation (Light/Dark Shadow Mapping)
| Level | Light Shadow | Dark Shadow |
|-------|-------------|-------------|
| 0 | none | none |
| 1 | 0 1px 2px rgba(0,0,0,0.1) | 0 1px 2px rgba(0,0,0,0.3) |
| 2 | 0 2px 4px rgba(0,0,0,0.1) | 0 3px 6px rgba(0,0,0,0.4) |
| 3 | 0 4px 8px rgba(0,0,0,0.1) | 0 6px 12px rgba(0,0,0,0.5) |
| 6 | 0 8px 16px rgba(0,0,0,0.12) | 0 12px 24px rgba(0,0,0,0.6) |
| 8 | 0 16px 32px rgba(0,0,0,0.12) | 0 24px 48px rgba(0,0,0,0.6) |

## Button Variants
### Filled Button
- Container: md-sys-color-primary
- Label: md-sys-color-on-primary
- Shape: md-sys-shape-corner-small (4px)
- Container height: 40dp

### Filled Tonal Button
- Container: md-sys-color-secondary-container
- Label: md-sys-color-on-secondary-container
- For less prominent actions

### Outlined Button
- Container: Transparent
- Border: 1px outline
- Label: md-sys-color-primary
- Shape: md-sys-shape-corner-small

### Text Button
- No container, no border
- Label only
- Ripples: md-sys-color-primary at 8% opacity

### FAB (Floating Action Button)
- Container: md-sys-color-primary
- Icon: md-sys-color-on-primary
- Shape: 16dp (custom) or circle (default 56dp)
- Elevation: 6dp (resting), 12dp (pressed)

## Input Field Variant Tokens
| Token | Filled | Outlined |
|-------|--------|----------|
| Container color | md-sys-color-surface-variant | Transparent |
| Active indicator | md-sys-color-primary | md-sys-color-primary |
| Text field label | md-sys-color-primary | md-sys-color-primary |
| Hint text | md-sys-color.on-surface-variant | md-sys-color.on-surface-variant |
| Cursor color | md-sys-color.primary | md-sys-color.primary |
| Selected glyph color | md-sys-color.primary | md-sys-color.primary |
| Error label | md-sys-color.error | md-sys-color.error |
| Error indicator | md-sys-color.error | md-sys-color.error |
| Helper text | md-sys-color.on-surface-variant | md-sys-color.on-surface-variant |
