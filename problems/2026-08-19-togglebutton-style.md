# Problem: Widget.Material3.Button.ToggleButton not found (aapt2 link error)

## Date: 2026-08-19
## Project: 608 guest injector

## Problem
`main.xml:360/370` → `resource style/Widget.Material3.Button.ToggleButton not found` during fork aapt2 resource linking.
V6 te scan depth toggle er jonno ei style use korechilam.

## Root cause
Fork er material library te `Widget.Material3.Button.ToggleButton` style **nei**. Material 1.13.0 (fork version) e available Button styles sudu: Widget.Material3.Button, .ElevatedButton, .IconButton*, .OutlinedButton, .TextButton, .TonalButton, .UnelevatedButton. (ToggleButton style shudhu aro newer/expressive version e thake.) Toke `app:checkedBackgroundColor` attr o nei (verified grep: checked* attrs = checkedButton/checkedIcon*/checkedState only).

## Solution
- Style → `Widget.Material3.Button.OutlinedButton` (official M3 segmented/toggle style).
- Checked visual AUTO hoy — `m3_text_button_background_color_selector` state_checked → `?attr/colorSecondaryContainer` (fill hoy checked hole), unchecked → `?attr/colorContainer`.
- `app:checkedBackgroundColor` remove (attr exist na — dile aro link error hoto).
- Toggle group attrs verified exist: checkedButton/selectionRequired/singleSelection.

## Files changed
- layout/main.xml (btn_quick/btn_deep style trog)

## Lesson
- CSS class lists vs M3 style names: **verify every `@style/Widget.Material3.*` + `app:` attr against material res/values/values.xml** BEFORE building. 
- Command: `grep style="@style/Widget.Material3.*" layout/main.xml` theke list → compare with `<style name="..."` in material jars' values.xml.
- M3 segmented button = OutlinedButton (check state apni handle kora lagbe na).

## Checked visuals (theme)
checked → secondaryContainer #DCE7C8 fill + primary #4C662B text (readable). Outlined stroke primary.
