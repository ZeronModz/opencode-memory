# Telegram Mini Apps — Complete Reference Summary

## Recent Changes Timeline

### April 3, 2026 — Bot API 9.6
- Added `WebApp.requestChat()` method

### March 1, 2026 — Bot API 9.5
- Added `BottomButton.iconCustomEmojiId` field

### July 3, 2025 — Bot API 9.1
- Added `WebApp.hideKeyboard()` method

### April 11, 2025 — Bot API 9.0
- Added `DeviceStorage` — persistent local storage on user's device
- Added `SecureStorage` — secure local storage for sensitive data

### November 17, 2024 — Bot API 8.0 (Largest update ever)
**Full-screen Mode:** `requestFullscreen`, `exitFullscreen`, `safeAreaInset`, `contentSafeAreaInset`, `isActive`, `isFullscreen`
**Homescreen Shortcuts:** `addToHomeScreen`, `checkHomeScreenStatus`
**Emoji Status:** `setEmojiStatus`, `requestEmojiStatusAccess`
**Media Sharing:** `shareMessage`, `downloadFile`, `shareToStory`
**Geolocation:** `LocationManager`
**Device Motion:** `Accelerometer`, `DeviceOrientation`, `Gyroscope`, `lockOrientation`, `unlockOrientation`
**Subscriptions & Gifts via Telegram Stars**
**Loading Screen Customization** via BotFather
**Android hardware info** in User-Agent

### September 6, 2024 — Bot API 7.10
- `SecondaryButton`, `secondaryButtonClicked`
- `MainButton` renamed to `BottomButton`
- `bottomBarColor`, `setBottomBarColor`
- `bottom_bar_bg_color` in ThemeParams

### July 31, 2024 — Bot API 7.8
- Main Mini App (bot profile button)
- `shareToStory` method

### July 7, 2024 — Bot API 7.7
- `isVerticalSwipesEnabled`, `enableVerticalSwipes`, `disableVerticalSwipes`
- `scanQrPopupClosed` event

### July 1, 2024 — Bot API 7.6
- `section_separator_color` in ThemeParams
- Direct Link Mini Apps default opening mode changed

### March 31, 2024 — Bot API 7.2
- `BiometricManager`

### December 29, 2023 — Bot API 7.0
- `SettingsButton`
- Theme fields: `header_bg_color`, `accent_text_color`, `section_bg_color`, `section_header_text_color`, `subtitle_text_color`, `destructive_text_color`
- Mini Apps no longer close on `openTelegramLink`

### September 22, 2023 — Bot API 6.9
- `CloudStorage`
- `requestWriteAccess`, `requestContact`
- WebAppUser: `added_to_attachment_menu`, `allows_write_to_pm`
- Events: `writeAccessRequested`, `contactRequested`
- Any header color with `setHeaderColor`

### April 21, 2023 — Bot API 6.7
- Launch from inline query results and direct link
- `switchInlineQuery`

### December 30, 2022 — Bot API 6.4
- `platform`, `openLink` with `options` param
- `showScanQrPopup`, `closeScanQrPopup`, `readTextFromClipboard`
- Events: `qrTextReceived`, `clipboardTextReceived`

### August 12, 2022 — Bot API 6.2
- `isClosingConfirmationEnabled`, `enableClosingConfirmation`, `disableClosingConfirmation`
- `showPopup`, `showAlert`, `showConfirm`
- `is_premium` in WebAppUser
- Event: `popupClosed`

### June 20, 2022 — Bot API 6.1
- `expand` method
- `BottomButton` color customization

---

## Launch Methods (7 ways)

1. **Main Mini App** — Bot profile button, set via BotFather: `/mybots > Select Bot > Bot Settings > Configure Mini App`
2. **Keyboard Button Mini App** — `KeyboardButton` with `web_app` field
3. **Inline Button Mini App** — `InlineKeyboardButton` with `web_app` field (opens Mini App inline, shows loading until `ready()`)
4. **Menu Button** — `setChatMenuButton` API method
5. **Inline Mode** — `switchInlineQuery` method or `switch_inline_query` in button
6. **Direct Link** — `https://t.me/YourBot/AppName` or `t.me/YourBot/AppName?startapp=param`
7. **Attachment Menu** — Added via BotFather, appears in attach menu in chats

---

## WebApp Object — `window.Telegram.WebApp`

### Properties

| Field | Type | API | Description |
|-------|------|-----|-------------|
| `initData` | string | 6.1 | Raw initialization data for auth validation |
| `initDataUnsafe` | WebAppInitData | 6.1 | Parsed init data (UNSAFE — do not trust client-side) |
| `version` | string | 6.1 | Current Bot API version |
| `platform` | string | 6.4 | Platform identifier (e.g. "android", "ios", "macos", "tdesktop", "webbrowser", "webk", "web") |
| `colorScheme` | string | 6.1 | "light" or "dark" |
| `themeParams` | ThemeParams | 6.1 | Current theme colors |
| `isActive` | boolean | 8.0 | Mini App is in foreground |
| `isFullscreen` | boolean | 8.0 | Mini App is in full-screen mode |
| `isOrientationLocked` | boolean | 8.0 | Screen orientation is locked |
| `safeAreaInset` | SafeAreaInset | 8.0 | Safe area insets |
| `contentSafeAreaInset` | ContentSafeAreaInset | 8.0 | Content safe area insets |
| `bottomBarColor` | string | 7.10 | Bottom bar color in #RRGGBB format |
| `headerColor` | string | 6.1 | Header color in #RRGGBB format |
| `isClosingConfirmationEnabled` | boolean | 6.2 | Whether closing confirmation is enabled |
| `isVerticalSwipesEnabled` | boolean | 7.7 | Whether vertical swipes are enabled |
| `BackButton` | BackButton | 6.1 | Back button control |
| `BottomButton` | BottomButton | 7.10 | Main action button (replaces MainButton) |
| `SecondaryButton` | BottomButton | 7.10 | Secondary action button |
| `SettingsButton` | SettingsButton | 7.0 | Settings button control |
| `HapticFeedback` | HapticFeedback | 6.1 | Haptic feedback |
| `CloudStorage` | CloudStorage | 6.9 | Cloud storage |
| `BiometricManager` | BiometricManager | 7.2 | Biometric auth |
| `Accelerometer` | Accelerometer | 8.0 | Accelerometer data |
| `DeviceOrientation` | DeviceOrientation | 8.0 | Device orientation data |
| `Gyroscope` | Gyroscope | 8.0 | Gyroscope data |
| `LocationManager` | LocationManager | 8.0 | Geolocation |
| `DeviceStorage` | DeviceStorage | 9.0 | Persistent local storage |
| `SecureStorage` | SecureStorage | 9.0 | Secure local storage |

### Methods

| Method | API | Params | Description |
|--------|-----|--------|-------------|
| `ready()` | 6.1 | — | Tells Telegram Mini App is ready (hides loading placeholder) |
| `expand()` | 6.1 | — | Expands Mini App to maximum available height |
| `close()` | 6.1 | — | Closes the Mini App |
| `setHeaderColor(color)` | 6.1 | string (#RRGGBB) | Sets header color (API 6.9+: any color, before: only "bg_color"/"secondary_bg_color") |
| `setBottomBarColor(color)` | 7.10 | string (#RRGGBB) | Sets bottom bar color |
| `enableClosingConfirmation()` | 6.2 | — | Show confirmation dialog on close |
| `disableClosingConfirmation()` | 6.2 | — | Hide confirmation dialog on close |
| `isVersionAtLeast(version)` | 6.1 | string | Checks if version meets minimum |
| `onEvent(eventType, callback)` | 6.1 | string, fn | Subscribe to event |
| `offEvent(eventType, callback)` | 6.1 | string, fn | Unsubscribe from event |
| `sendData(data)` | 6.1 | string | Sends data to bot (for keyboard button Mini Apps) |
| `switchInlineQuery(query, chatTypes)` | 6.7 | string, string[] | Switch to inline mode |
| `openLink(url, options)` | 6.1/6.4 | string, {try_instant_view?: boolean} | Open link (6.4+: optional options param) |
| `openTelegramLink(url)` | 6.1 | string | Open Telegram link internally |
| `openInvoice(url, callback)` | 6.1 | string, fn | Open invoice for payment |
| `showPopup(params, callback)` | 6.2 | PopupParams, fn | Show popup |
| `showAlert(message, callback)` | 6.2 | string, fn | Show alert |
| `showConfirm(message, callback)` | 6.2 | string, fn | Show confirm dialog |
| `showScanQrPopup(params, callback)` | 6.4 | ScanQrPopupParams, fn | Show QR scanner |
| `closeScanQrPopup()` | 6.4 | — | Close QR scanner |
| `readTextFromClipboard(callback)` | 6.4 | fn | Read clipboard text |
| `requestWriteAccess(callback)` | 6.9 | fn | Request write access |
| `requestContact(callback)` | 6.9 | fn | Request phone number |
| `shareToStory(params)` | 7.8 | StoryShareParams | Share to story |
| `shareMessage(msg_id, callback)` | 8.0 | string, fn | Share media to chats |
| `downloadFile(params)` | 8.0 | DownloadFileParams | Download file via native popup |
| `requestFullscreen(callback)` | 8.0 | fn | Request full-screen mode |
| `exitFullscreen()` | 8.0 | — | Exit full-screen mode |
| `lockOrientation()` | 8.0 | — | Lock screen orientation |
| `unlockOrientation()` | 8.0 | — | Unlock screen orientation |
| `addToHomeScreen()` | 8.0 | — | Add shortcut to home screen |
| `checkHomeScreenStatus(callback)` | 8.0 | fn | Check home screen shortcut status |
| `setEmojiStatus(params, callback)` | 8.0 | EmojiStatusParams, fn | Set custom emoji status |
| `requestEmojiStatusAccess(callback)` | 8.0 | fn | Request permission to update emoji status |
| `enableVerticalSwipes()` | 7.7 | — | Enable vertical swipes |
| `disableVerticalSwipes()` | 7.7 | — | Disable vertical swipes |
| `hideKeyboard()` | 9.1 | — | Hide the keyboard (mobile) |
| `requestChat(chatTypes, callback)` | 9.6 | string[], fn | Request a chat selection |

---

## ThemeParams

| Field | CSS Variable | Description |
|-------|-------------|-------------|
| `bg_color` | --tg-bg-color | Background color |
| `secondary_bg_color` | --tg-secondary-bg-color | Secondary background |
| `text_color` | --tg-text-color | Text color |
| `hint_color` | --tg-hint-color | Hint text color |
| `link_color` | --tg-link-color | Link color |
| `button_color` | --tg-button-color | Button color |
| `button_text_color` | --tg-button-text-color | Button text color |
| `header_bg_color` | --tg-header-bg-color | Header background (7.0) |
| `accent_text_color` | --tg-accent-text-color | Accent text (7.0) |
| `section_bg_color` | --tg-section-bg-color | Section background (7.0) |
| `section_header_text_color` | --tg-section-header-text-color | Section header text (7.0) |
| `subtitle_text_color` | --tg-subtitle-text-color | Subtitle text (7.0) |
| `destructive_text_color` | --tg-destructive-text-color | Destructive text (7.0) |
| `section_separator_color` | --tg-section-separator-color | Section separator (7.6) |
| `bottom_bar_bg_color` | --tg-bottom-bar-bg-color | Bottom bar background (7.10) |

> Also available via CSS variables: var(--tg-bg-color), etc.

---

## Supporting Classes

### WebAppInitData
| Field | Type | Description |
|-------|------|-------------|
| `query_id` | string | Unique query ID |
| `user` | WebAppUser | User data |
| `receiver` | WebAppUser | Receiver user data |
| `chat` | WebAppChat | Chat data (group Mini Apps) |
| `chat_type` | string | "sender", "private", "group", "supergroup", "channel" |
| `chat_instance` | string | Global chat identifier |
| `start_param` | string | Value of `startapp` or `startattach` parameter |
| `can_send_after` | number | Time until next message can be sent (in seconds) |
| `auth_date` | number | Uniauthorization date |
| `hash` | string | HMAC-SHA256 signature for validation |

### WebAppUser
| Field | Type | Description |
|-------|------|-------------|
| `id` | number | User ID |
| `is_bot` | boolean | Is bot |
| `first_name` | string | First name |
| `last_name` | string | Last name |
| `username` | string | Username |
| `language_code` | string | IETF language tag |
| `is_premium` | boolean | Premium user (6.2) |
| `added_to_attachment_menu` | boolean | Added bot to attachment menu (6.9) |
| `allows_write_to_pm` | boolean | Allows bot to message (6.9) |
| `photo_url` | string | Profile photo URL (8.0, previously limited) |

### WebAppChat
| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Chat ID |
| `type` | string | "group", "supergroup", "channel" |
| `title` | string | Chat title |
| `username` | string | Username |
| `photo_url` | string | Chat photo URL |

### BottomButton (formerly MainButton)
| Field/Method | Type | Description |
|-------------|------|-------------|
| `text` | string | Button text |
| `color` | string | Button color (#RRGGBB) |
| `textColor` | string | Text color (#RRGGBB) |
| `isVisible` | boolean | Visibility |
| `isActive` | boolean | Active state |
| `isProgressVisible` | boolean | Show loading spinner |
| `iconCustomEmojiId` | string | Custom emoji icon (9.5) |
| `show()` | — | Show button |
| `hide()` | — | Hide button |
| `enable()` | — | Enable button |
| `disable()` | — | Disable button |
| `setText(text)` | — | Set text |
| `onClick(callback)` | — | Set click handler |
| `offClick(callback)` | — | Remove click handler |
| `showProgress(leaveActive)` | — | Show loading spinner |
| `hideProgress()` | — | Hide loading spinner |
| `setParams(params)` | — | Set multiple params at once |

### BackButton
| Method | Description |
|--------|-------------|
| `show()` | Show back button |
| `hide()` | Hide back button |
| `onClick(callback)` | Set click handler |
| `offClick(callback)` | Remove click handler |

### SettingsButton
| Method | Description |
|--------|-------------|
| `show()` | Show settings button (7.0+) |
| `hide()` | Hide settings button (7.0+) |
| `onClick(callback)` | Set click handler |
| `offClick(callback)` | Remove click handler |

### HapticFeedback
| Method | Params | Description |
|--------|--------|-------------|
| `impactOccurred(style)` | "light"|"medium"|"heavy"|"rigid"|"soft" | Impact feedback |
| `notificationOccurred(type)` | "error"|"success"|"warning" | Notification feedback |
| `selectionChanged()` | — | Selection changed feedback |

### CloudStorage
| Method | Description |
|--------|-------------|
| `setItem(key, value, callback)` | Store string value |
| `getItem(key, callback)` | Get string value |
| `getItems(keys, callback)` | Get multiple items |
| `removeItem(key, callback)` | Remove item |
| `removeItems(keys, callback)` | Remove multiple items |
| `getKeys(callback)` | Get all keys |

### BiometricManager
| Field/Method | Description |
|-------------|-------------|
| `isInited` | Whether initialized |
| `isBiometricAvailable` | Whether biometric auth available |
| `biometricType` | "finger", "face" or "unknown" |
| `isAccessRequested` | Whether access was requested |
| `isAccessGranted` | Whether access was granted |
| `isBiometricReady` | Whether biometric is ready to auth |
| `deviceId` | Unique device identifier |
| `init(params, callback)` | Initialize |
| `requestAccess(params, callback)` | Request biometric access |
| `authenticate(params, callback)` | Authenticate user |
| `updateBiometricToken(token, callback)` | Update biometric token |
| `openSettings()` | Open biometric settings |

### Accelerometer (8.0)
| Field/Method | Description |
|-------------|-------------|
| `isStarted` | Whether started |
| `x` | Acceleration in X-axis (m/s^2) |
| `y` | Acceleration in Y-axis |
| `z` | Acceleration in Z-axis |
| `start(params, callback)` | Start tracking |
| `stop(callback)` | Stop tracking |

### DeviceOrientation (8.0)
| Field/Method | Description |
|-------------|-------------|
| `isStarted` | Whether started |
| `alpha` | Rotation around Z-axis (degrees) |
| `beta` | Rotation around X-axis |
| `gamma` | Rotation around Y-axis |
| `absolute` | Whether absolute values |
| `start(params, callback)` | Start tracking |
| `stop(callback)` | Stop tracking |

### Gyroscope (8.0)
| Field/Method | Description |
|-------------|-------------|
| `isStarted` | Whether started |
| `x` | Angular velocity around X (rad/s) |
| `y` | Angular velocity around Y |
| `z` | Angular velocity around Z |
| `start(params, callback)` | Start tracking |
| `stop(callback)` | Stop tracking |

### LocationManager (8.0)
| Method | Description |
|--------|-------------|
| `getLocation(callback)` | Request location (shows native dialog) |
| `openSettings()` | Open location settings |

### LocationData (8.0)
| Field | Type | Description |
|-------|------|-------------|
| `latitude` | number | Latitude |
| `longitude` | number | Longitude |
| `altitude` | number | Altitude |

### DeviceStorage (9.0)
| Method | Description |
|--------|-------------|
| `get(key, callback)` | Get stored value |
| `set(key, value, callback)` | Store value |
| `remove(key, callback)` | Remove value |
| `clear(callback)` | Clear all values |
| `keys(callback)` | Get all keys |
| `size(callback)` | Get number of stored entries |

### SecureStorage (9.0)
| Method | Description |
|--------|-------------|
| `get(key, callback)` | Get securely stored value |
| `set(key, value, callback)` | Store value securely |
| `remove(key, callback)` | Remove value |
| `clear(callback)` | Clear all values |
| `keys(callback)` | Get all keys |
| `size(callback)` | Get number of entries |

### SafeAreaInset / ContentSafeAreaInset (8.0)
Both have: `top`, `bottom`, `left`, `right` (number in px)

### PopupParams
| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Popup title |
| `message` | string | Popup message |
| `buttons` | PopupButton[] | Array of buttons |

### PopupButton
| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Button identifier (returned in callback) |
| `type` | string | "default"|"ok"|"close"|"cancel"|"destructive" |
| `text` | string | Button text (for "default"/"destructive" types) |

### ScanQrPopupParams
| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Text displayed above QR scanner |

### StoryShareParams
| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Story text |
| `widget_link` | StoryWidgetLink | Optional widget link |

### StoryWidgetLink
| Field | Type | Description |
|-------|------|-------------|
| `url` | string | Widget URL |
| `name` | string | Widget name |

### DownloadFileParams
| Field | Type | Description |
|-------|------|-------------|
| `url` | string | File URL |
| `file_name` | string | Suggested file name |

### EmojiStatusParams
| Field | Type | Description |
|-------|------|-------------|
| `custom_emoji_id` | string | Custom emoji ID |
| `duration` | number | Duration in seconds (optional) |

---

## Events Available for Mini Apps

| Event | API | Callback data | Description |
|-------|-----|--------------|-------------|
| `mainButtonClicked` | 6.1 | — | Main/primary button clicked |
| `secondaryButtonClicked` | 7.10 | — | Secondary button clicked |
| `backButtonClicked` | 6.1 | — | Back button clicked |
| `settingsButtonClicked` | 7.0 | — | Settings button clicked |
| `popupClosed` | 6.2 | {button_id: string} | Popup dismissed |
| `qrTextReceived` | 6.4 | string | QR code scanned |
| `scanQrPopupClosed` | 7.7 | — | QR scanner closed |
| `clipboardTextReceived` | 6.4 | string | Clipboard text received |
| `writeAccessRequested` | 6.9 | {status: string} | Write access request result |
| `contactRequested` | 6.9 | {status: string} | Contact request result |
| `biometricManagerUpdated` | 7.2 | — | Biometric manager state changed |
| `biometricAuthRequested` | 7.2 | {isAuthenticated: boolean} | Biometric auth result |
| `biometricTokenUpdated` | 7.2 | {isUpdated: boolean} | Biometric token updated |
| `accelerometerStarted` | 8.0 | — | Accelerometer started |
| `accelerometerStopped` | 8.0 | — | Accelerometer stopped |
| `accelerometerChanged` | 8.0 | — | Accelerometer data changed |
| `accelerometerFailed` | 8.0 | {error: string} | Accelerometer error |
| `deviceOrientationStarted` | 8.0 | — | Device orientation started |
| `deviceOrientationStopped` | 8.0 | — | Device orientation stopped |
| `deviceOrientationChanged` | 8.0 | — | Device orientation changed |
| `deviceOrientationFailed` | 8.0 | {error: string} | Device orientation error |
| `gyroscopeStarted` | 8.0 | — | Gyroscope started |
| `gyroscopeStopped` | 8.0 | — | Gyroscope stopped |
| `gyroscopeChanged` | 8.0 | — | Gyroscope changed |
| `gyroscopeFailed` | 8.0 | {error: string} | Gyroscope error |
| `locationManagerUpdated` | 8.0 | — | Location manager updated |
| `locationRequested` | 8.0 | {locationData: LocationData} | Location obtained |
| `shareMessageSent` | 8.0 | — | Message shared successfully |
| `shareMessageFailed` | 8.0 | — | Message share failed |
| `fileDownloadRequested` | 8.0 | {status: string} | File download requested |
| `homeScreenAdded` | 8.0 | {status: string} | Home screen shortcut added |
| `homeScreenChecked` | 8.0 | {status: string} | Home screen status checked |
| `fullscreenChanged` | 8.0 | {isFullscreen: boolean} | Full-screen mode changed |
| `fullscreenFailed` | 8.0 | {error: string} | Full-screen request failed |
| `safeAreaChanged` | 8.0 | — | Safe area insets changed |
| `contentSafeAreaChanged` | 8.0 | — | Content safe area changed |
| `activated` | 8.0 | — | Mini App brought to foreground |
| `deactivated` | 8.0 | — | Mini App sent to background |
| `emojiStatusSet` | 8.0 | — | Emoji status set |
| `emojiStatusFailed` | 8.0 | — | Emoji status failed |
| `emojiStatusAccessRequested` | 8.0 | {status: string} | Emoji status access result |
| `themeChanged` | 6.1 | — | Theme changed (light/dark switch) |
| `viewportChanged` | 6.1 | {height: number, isStateStable: boolean} | Viewport changed |
| `invoiceClosed` | 6.1 | {url: string, status: string} | Invoice closed |

---

## Data Validation

### HMAC-SHA256 Validation
1. Create secret key: `HMAC_SHA256("WebAppData", bot_token)`
2. Create data-check-string: Sort all fields alphabetically by key, format as `key=value\n`, exclude `hash`
3. Compute `HMAC_SHA256(secret, data_check_string)` in hex
4. Compare with `hash` field — must match (use constant-time comparison)

### Third-Party Validation (8.0)
Third parties can validate without knowing bot token:
1. Compute `HMAC_SHA256("WebAppData", "WebAppData")` → secret key
2. Same data_check_string process as above
3. Compare hash

---

## Design Guidelines

- Support both light and dark color schemes using theme params
- Use CSS variables: `var(--tg-bg-color)`, `var(--tg-text-color)`, etc.
- Respect `SafeAreaInset` and `ContentSafeAreaInset` for notched devices
- Use relative sizing (vh/vw/% not px) since viewport changes
- Minimum width: 375px (though can vary)
- Consider `isActive`/`activated`/`deactivated` events for pausing/resuming
- Loading screen: custom icon + colors configurable via BotFather
- Full-screen: request after user interaction; portrait and landscape support

---

## Debug Mode for Mini Apps

**Telegram Desktop**: Right-click → "Inspect Element"
**Android**: Enable "Debug Mini Apps" in Settings → Advanced → Experimental
**iOS (new in 8.0)**: Enable "Debug Mini Apps" in Settings → Advanced → Experimental
Open Mini App → debug console appears

---

## Test Environment

- Use `@BotFather` → `/setprivacy` → Disable (to see all messages)
- Create test bot with `@BotFather`
- Use separate bot token for test environment (Bot API test servers)
- Test server: `https://api.telegram.org/bot<token>/METHOD`
- Webhook: test servers accept self-signed certificates easily

---

## Attachment Menu

- Configured via BotFather: `/mybots > Select Bot > Bot Settings > Configure Mini App > Select Attachment Menu`
- Bot must have at least 1 Mini App configured
- `WebAppUser.added_to_attachment_menu` detects whether user added it
- Settings button in Mini App can be used to manage attachment menu

---

## Additional Data in User-Agent (8.0, Android only)

Format: `Telegram-Android/<AppVersion> (<SDKVersion>; <DeviceModel>; <OSVersion>; PerformanceClass=<class>)`

Performance classes: 0 (low-end) to 3 (high-end), based on SoC.
