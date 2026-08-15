# Java Gotcha: Object[][] literal er inner arrays — REMAIN Object[]

## Date: 2026-08-15 (Project 607 button bug)

## Problem
```java
Object[][] rows = {
    {"📍 Location", "do:loc", "📷 Photo", "do:pic"},   // ekhane Java ei inner ko er typedab Object[] e thake
    ...
};
for (Object o : row) { if (o instanceof String[]) { ... } }  // ALWAYS FALSE!
```
When object outer array declared as `Object[][]`, inner braced-literal `{"a","b"}` creates **Object[]**, NOT String[].
Derfor `instanceof String[]` → false → sob button skip → `{"inline_keyboard":[]}` → **message text ashe kintu buttons NA**.

`{"inline_keyboard":[]}` empty-as(format asbab shob valid thakleo Telegram khali keyboard pai — message text show hoy, button nai) — eta dhora pore na, khar same.

## Fix (proven)
Interpret each row as **flat pairs**: `[text, callback_data, text, callback_data, ...]`.
```java
for (Object[] r : rows) {
    for (int i = 0; i + 1 < r.length; i += 2) {
        String text = String.valueOf(r[i]);
        String data = String.valueOf(r[i+1]);
        row.append("{\"text\":\"").append(jesc(text))
           .append("\",\"callback_data\":\"").append(jesc(data)).append("\"}");
    }
}
```
(Ek row te joto pairs → toto buttons.)

## Verification method
- Host e pure-Java test file build kore output print (android jar chhara) — `Object[][]` literal diye same shape.
- Tarpor exact JSON curl diye live Telegram API te pathae → `{"ok":true, "reply_markup":{...}}` — buttons server-side attached confirm.

## Rule (remember forever)
- Java e `Object[][] x = {{"a","b"}, {"c","d"}}` → inner items are `Object[]` (element-wise String), kono `String[]` nei.
- Array initializer e nested button objects chai le: `new String[]{"a","b"}` explicit lihon, na hole flat-pair interpretation.
- Inline keyboard building: JSON **manually** construct kora safer (org.json/jackson dependency chhara) — `jesc()` esaaper helper (", \, \n).