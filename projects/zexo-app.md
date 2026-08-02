# Zexo — Telegram Bot Client (Android, C++ core)

## Status: v1 COMPLETE — APK Built ✓

## Date: 2026-08-02 23:51

## Overview
Android app **Zexo** — Telegram Bot Client. Login with bot token, chat with any chat, edit/delete messages, bot profile editing, webhook management. Inspired by ir.ilmili.telegraph but for BOT accounts (not user accounts).

## Project Info
- **APK**: `/data/data/com.termux/files/home/Zexo.apk` (8.1MB, debug)
- **Also**: `/storage/emulated/0/Download/Zexo.apk`
- **Package**: `com.devzeron.zexo`
- **App name**: Zexo
- **minSdk**: 24, targetSdk 34, compileSdk 36
- **Language**: Kotlin (UI) + C++ (core via JNI), NDK r29
- **Source**: `/data/data/com.termux/files/home/zexo`

## Architecture
- **C++ core** (`app/src/main/cpp/`):
  - `json.h` — header-only JSON parser/writer (zjson namespace)
  - `tgbot.h/.cpp` — Telegram Bot API client (HTTP via JNI → Kotlin HttpBridge)
  - `native-lib.cpp` — JNI exports (`NativeCore` object methods)
  - CMake target: `zexo` (libzexo.so)
- **Kotlin**:
  - `HttpBridge.kt` — HttpURLConnection wrapper, called from C++ via JNI
  - `NativeCore.kt` — JNI declarations (nativeGetMe, nativeGetUpdates, etc.)
  - `Session.kt` — SharedPreferences token/bot storage
  - `TelegramStore.kt` — in-memory chats/messages, getUpdates processing
  - `MainActivity.kt` — bottom nav + long polling thread
  - `ChatsFragment.kt` — chat list + new chat by ID dialog
  - `ChatActivity.kt` — messages, send, edit/delete, inline keyboards
  - `ProfileFragment.kt` — bot info + edit name/desc/shortdesc/commands
  - `SettingsFragment.kt` — webhook info/delete, silent send, dark mode, logout

## Design
- **Material 3** everywhere (Theme.Material3.DayNight.NoActionBar)
- **Colors**: from `/storage/emulated/0/Download/material-theme.zip` (olive/green M3 scheme) → `values/colors.xml` + `values-night/colors.xml`
- M3 components: MaterialCardView, MaterialButton, TextInputLayout, Chip, Switch, BottomNavigationView, ExtendedFAB, MaterialToolbar, MaterialDivider, MaterialAlertDialogBuilder

## JNI Methods (C++ ↔ Kotlin)
| Method | C++ |
|--------|-----|
| getMe | nativeGetMe |
| getUpdates | nativeGetUpdates |
| sendMessage | nativeSendMessage |
| editMessageText | nativeEditMessage |
| deleteMessage | nativeDeleteMessage |
| getChat | nativeGetChat |
| getChatMemberCount | nativeGetChatMemberCount |
| setMyName/Description/ShortDescription | nativeSetMy* |
| setMyCommands/getMyCommands | nativeSet/GetMyCommands |
| getWebhookInfo | nativeGetWebhookInfo |
| deleteWebhook | nativeDeleteWebhook |
| answerCallbackQuery | nativeAnswerCallback |

## Build Commands
```bash
cd /data/data/com.termux/files/home/zexo
./gradlew :app:assembleDebug
# output: app/build/outputs/apk/debug/app-debug.apk
```

## Issues Fixed
- `Value(true)` needed bool constructor in json.h
- `long` vs `int64_t` duplicate constructor (same type on Android) — removed `long` ctor
- `lateinit var chatId: Long` → `var chatId = 0L` (primitive not allowed with lateinit)

## Features Done (v1)
- [x] Login with bot token (validates via getMe)
- [x] Chat list from getUpdates long polling (8s interval)
- [x] Chat screen: send, edit (long-press), delete, inline keyboard buttons
- [x] New chat by ID (getChat)
- [x] Bot profile view + edit name/description/short_description/commands
- [x] Webhook info, delete webhook
- [x] Dark mode toggle, silent send toggle
- [x] Logout clears session

## Next Steps (v2 ideas)
- Bot profile photo (getUserProfilePhotos + sendPhoto via multipart)
- Media send (photos via multipart — needs C++ multipart builder)
- Edit message via keyboard callback
- Forward messages, reply threads
- Persist chats/messages locally (JSON file)
- Splash screen, app icon custom
- Notification service for new messages
