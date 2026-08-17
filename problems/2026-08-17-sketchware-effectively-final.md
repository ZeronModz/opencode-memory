# 2026-08-17 — Sketchware Java: "effectively final" compile error in anonymous class

## Problem
Sketchware Pro / old javac project e anonymous inner class er moddhe local variable use korle:

```
ERROR: Cannot refer to the non-final local variable lp defined in an enclosing scope
```

Er 13 ta error (1 `setOnTouchListener` e hyperbole Variable `lp` `root` access.)

## Root cause
- Java 8+ anonymous class e modifiable local variable access korte gele "effectively final" requirement.
- Tomader javac e oi sugar support kore na — explicit `final` dite hobe.

## Solution
Anonymous class-er **age** `final` alias banaiye dewa:

```java
final View fRoot = root;
final WindowManager.LayoutParams fLp = lp;
root.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent ev) {
        fLp.x = ...;                       // field mutation final ref-e allowed
        overlayWm.updateViewLayout(fRoot, fLp);
        return true;
    }
});
```

## Files changed
- `/storage/emulated/0/.sketchware/data/607/files/java/TelegramRemoteService.java` — `showAlertBanner()` e OnTouchListener.

## Verify
- `javac -source 1.8 -target 1.8` + android-34.jar + okhttp3 → clean, 61 class.
- Source 7 e compile hoina (JDK e support nai), source 8 lagbe/aze.

## Lesson
Sketchware/old javac e anonymous class e kono local var lagle sb-somooy age `final fVar = var;` banaye nin. Always use Java 8 semantics.