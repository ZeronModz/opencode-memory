# 🤖 zeron-telebot — Sketchware Telegram Remote Control

## Status: 2026-08-15 — Active
Project: `.sketchware/` #607, package `mata.pro`
Build tool: Sketchware Pro v7.0.0-daydream-android33

## Purpose
2 phone setup: home phone e APK + Telegram bot → haat er phone theke emergency control.

## Architecture
- **TelegramRemoteService** — foreground service, long-polls `getUpdates` (offset tracking), executes commands, replies via bot API.
- **GetSmsAndCalls** (existing) — SMS + incoming call events → Telegram (pre-existing).
- **MyNotificationListenerService** (existing + modified) — all notifications → Telegram. `onListenerConnected()` e remote service start hoi.
- **AutoStartReceiver** — BOOT_COMPLETED/MY_PACKAGE_REPLACED/USER_PRESENT e service + perms.
- **PermissionHelperActivity** — transparent runtime-perm prompter.
- Config: `assets/server.json` (bot_token, chat_id, urlweb).

## Commands (2026-08-15 build)
/start /help /loc /pic /video N /audio N /batt /ring N /ring stop /flash on|off /sms /ip

## Key files
- `data/607/files/java/{TelegramRemoteService,AutoStartReceiver,PermissionHelperActivity,GetSmsAndCalls,MyNotificationListenerService}.java`
- `data/607/Injection/androidmanifest/app_components.txt`
- `data/607/permission`

## Notes / limitations
- Camera = deprecated `android.hardware.Camera` API (simple, works on most devices).
- No true live streaming via Bot API — on-demand photo/video.
- Android 14: FGS types camera|microphone|location declared; still may refuse camera FGS start from background on strict OEMs.
- Screen capture (MediaProjection) not implemented — needs one-time system consent.
- Battery optimization OFF recommended or OEM killer stops service.

## TODO
- Build & test in Sketchware Pro.
- Main UI (binary view/logic) untouched — launcher shows old promo screen.