# @material/web 2.5.0 — Component API cheat sheet (2026-07-31)

## Loading (esm.run import map — all verified 200)
```json
{ "imports": {
  "@material/web/": "https://esm.run/@material/web@2.5.0/",
  "@material/material-color-utilities": "https://esm.run/@material/material-color-utilities@0.3.0"
}}
```
`all.js` covers core. Labs NOT in all.js → import separately:
`labs/navigationdrawer/navigation-drawer.js` + `navigation-drawer-modal.js`
`labs/navigationbar/navigation-bar.js` + `labs/navigationtab/navigation-tab.js`
`labs/card/{elevated,filled,outlined}-card.js`
`labs/badge/badge.js`
Plus `typography/md-typescale-styles.js`.

## Layout / nav
- `md-navigation-drawer` (persistent) & `md-navigation-drawer-modal`: props `opened`, `pivot` ('start'|'end'). Persistent = fixed, so offset content w/ margin. Modal shows scrim + closes on scrim/link.
- `md-navigation-bar`: prop `activeIndex`; event `navigation-bar-activated` with `e.detail.activeIndex`. Child: `md-navigation-tab` (`label`, `badgeValue`, icon via `<md-icon slot="icon">`).
- `md-list` + `md-list-item`: `type="link"` + `href` for links; slots `start` (icon), `headline`.
- `md-divider`, `md-badge` (value attr).
- `md-fab`: icon via `<md-icon slot="icon">`.

## Buttons / inputs
- Buttons: `md-filled-button`, `md-outlined-button`, `md-filled-tonal-button`, `md-text-button`, `md-elevated-button`. Props `href`, `target`, `download`, `trailingIcon`. Icons: `<md-icon slot="icon">` / `slot="trailing-icon"`.
- `md-icon-button`: `toggle`, `selected`; `md-filled-icon-button` etc.
- Text fields: `md-filled-text-field`, `md-outlined-text-field` — `.value`, `type`, `label`, `placeholder`, `errorText`; slots `leading-icon`/`trailing-icon`.
- `md-outlined-select` / `md-filled-select`: `.value`, `label`; options `<md-select-option value="x" selected><div slot="headline">Label</div></md-select-option>`.
- `md-checkbox`: `.checked`; `md-switch`: `.selected`, `icons`, `showOnlySelectedIcon`; `md-slider`: `.value`, `min/max/step`, `labeled`, `ticks`, event `input`; `md-radio`: `.checked` prop.
  - ⚠️ `md-radio:checked` CSS does NOT match custom elements — find via `.find(r => r.checked)`.

## Chips / menus / dialogs / progress / tabs
- `md-chip-set` container. `md-assist-chip`, `md-suggestion-chip` (`label` + icon slot), `md-filter-chip` (`.selected` toggles, click handler), `md-input-chip`.
- `md-menu` (`anchor="elementId"`, `.open`), `md-menu-item` (`.value`, `headline` slot, `start` icon slot).
- `md-dialog`: `.open = true`; form `method="dialog"` + `md-text-button form="..." value="close"` closes; event `closed`.
- `md-linear-progress`: `.value`/`max` (fraction), `buffer`; `md-circular-progress`: `indeterminate`.
- `md-tabs`: `activeTabIndex`, event `change` (`e.target.activeTabIndex`); `md-primary-tab`/`md-secondary-tab` with `data-lang`/`active`.

## Theming (dynamic color)
```js
const theme = themeFromSourceColor(argbFromHex(SEED));
// theme.schemes.dark / .light → toJSON() → {primary, onPrimary, primaryContainer, ... surface, surface-container-low/high...}
// set root.style.setProperty('--md-sys-color-'+k, hexFromArgb(v))
```
Also set `--md-ref-typeface-brand/plain`, `color-scheme`. `md-icon` font: set `--md-icon-font: 'Material Symbols Rounded'` + Google Fonts link; `md-icon` uses `font-variation-settings`.

## Gotchas
- esm.run returns 301 → jsdelivr +esm (use `curl -L`).
- material-web.dev docs slugs differ from component names: use `/components/list/`, `/tabs/`, `/text-field/`, `/select/`, `/progress/`, `/chip/`, `/fab/`. `/components/navigation-drawer/` etc are 404 (labs = roadmap).
- No snackbar/banner/top-app-bar in library yet (roadmap) → custom toast/appbar with CSS.
