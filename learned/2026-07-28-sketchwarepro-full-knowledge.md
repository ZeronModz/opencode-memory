# Sketchware Pro — Complete Knowledge Base

## Overview
- **What it is:** Android app builder (drag-drop + block-based programming), free & open-source
- **GitHub:** github.com/Sketchware-Pro/Sketchware-Pro
- **Stars:** 1668 | **Downloads:** 1.2M+ | **Forks:** 644
- **Community:** Discord (discord.gg/Dc8ZDBRK5V), Telegram (t.me/sketchwarepro)
- **Developer:** NiceSapien (last update: Feb 2024)
- **Platform:** Android-only (Termux/compatible)

## Core Features
- Block-based programming (no coding required)
- Drag-and-drop UI designer
- Android Studio export
- Enhanced UI editor
- Third-party library integration (local library system)
- Custom Blocks — create, use, and share
- No ads (Pro version is free)
- Kotlin Code Support (requires Android 8+/minApi26)
- PC-independent — full dev cycle on smartphone
- AppCompat support for Material Design

## Views (UI Components)
Views are graphical building blocks of the app's UI.

### Layouts (Containers)
- **Linear (V)** — vertical linear layout
- **Linear (H)** — horizontal linear layout
- **ScrollView** — scrollable container
- **RelativeLayout** — relative positioning

### Widgets
- **Button** — standard button
- **MaterialButton** — Material Design button (requires AppCompat)
- **TextView** — display text, labels, headings
- **EditText** — text input field
- **ImageView** — display images
- **CheckBox** — checkbox
- **Switch** — on/off switch
- **RadioButton** — radio button
- **Spinner** — dropdown selection
- **WebView** — web content
- **SeekBar** — slider
- **ProgressBar** — loading indicator
- **CalendarView** — date picker
- **RatingBar** — rating component
- **VideoView** — video player
- **SearchView** — search bar
- **GridView** — grid layout
- **AutoComplete** — autocomplete EditText
- **MultiAutoComplete** — multi-autocomplete EditText
- **ViewPager** — swipeable pages
- **BadgeView** — notification badge

### Special
- **ListView** — scrollable list (with customView support)
- **BottomNavigation** — Material bottom nav bar (requires AppCompat)
- **YouTube Player** — YouTube video player
- **FAB** — Floating Action Button
- **Image Manager** — manage app images/icons

### Properties (common to all views)
- width (match_parent/wrap_content/fixed)
- height
- padding/margin
- gravity
- background color
- visibility (visible/invisible/gone)

## Variables & Data Types
- **String** — text
- **Number** — integer/decimal
- **Boolean** — true/false
- **Map** — key-value pairs (like JSON object)
- **List Number** — ordered list of numbers
- **List String** — ordered list of strings
- **List Map** — ordered list of maps (like JSON array)

### Variable Blocks
- set String/Number/Boolean to value
- Number increase 1 / decrease 1
- Map: create new map, put (string/number/double/boolean/map/list), get, is empty, contain key/value, size, remove key, clear, get all keys to List String

### List Blocks
- List Number: contains, get at, index, add, insert, set, sort
- List String: contains, index, get, add, insert, set, sort, addAll
- List Map: contains at, get value at key, get Map at, add key-value, insert, set, add Map, insert Map, delete Map, sort (by key, isNumber, asc/desc)
- General: length, delete at, clear, reverse, shuffle, swap positions

## Events (Programming Logic)
Events trigger code when something happens:
- **onCreate** — app starts
- **onStart** — app becomes visible
- **onClick** — button/view is clicked
- **onLongClick** — long press
- **onNavigationItemSelected** — bottom nav item selected
- **onBindCustomView** — ListView custom view binding
- **onCheckedChanged** — checkbox/switch toggled
- **onItemSelected** — spinner item selected
- Timer events, etc.

### More Blocks (Reusable Code)
- Create custom functions within a single activity (like methods)
- Void, String, Number, Boolean, Map, List String, List Map, View return types
- Parameters: variables + views + components
- Scope: activity-level only (not global)

### Custom Blocks (Global Reusable Blocks)
- Created via Developer Tools → Block Manager
- Create palettes with custom colors
- Block Spec defines appearance (text, string, code, number, boolean inputs)
- Types: regular, c (if), e (if-else), s (string), b (boolean), d (number), v (variable), a (map), f (stop), l (list), p (component)
- Export/Import blocks via .json files
- Share blocks with community

## Components
Components add functionality without writing code:

### General Components
- **Intent** — navigate between activities, open websites
- **SharedPreferences** — persist data (key-value storage), file name based
- **Calendar** — date/time picker
- **Vibrator** — haptic feedback
- **Timer** — countdown/scheduling
- **Dialog** — custom dialogs
- **MediaPlayer** — audio playback
- **SoundPool** — sound effects
- **ObjectAnimator** — animations
- **Camera** — camera capture
- **FilePicker** — file selection
- **RequestNetwork** — HTTP requests
- **TextToSpeech** — TTS output
- **LocationManager** — GPS/location
- **ProgressDialog** — loading dialogs
- **TimePickerDialog** — time picker
- **Notification** — push notifications
- **VideoAd** — video ads

### Firebase Components
- **Firebase DB** — Realtime Database
- **Firebase Auth** — Authentication (email, phone, Google)
- **Firebase Storage** — cloud storage

### Google Components
- **AdMob** — banner/interstitial/rewarded ads
- **Google Login** — Google Sign-In
- **Phone Auth** — phone verification
- **Notification** — FCM push notifications
- **Dynamic Links** — deep linking

## Course Projects (Built-in Tutorials)
1. **Basics** — Views, Events, Running App, Components (Intent example)
2. **Notes App** — ListView, Custom View, FAB, SharedPreferences, persistent data
3. **Click Counter** — Multiple activities, Material Button, AppCompat, SharedPreferences for high scores
4. **Cool Things** — Advanced features exploration

## Compilation & Building
- Click "Run" → APK build
- Install directly on device
- Play Protect warning (install anyway)
- APK export available
- Android Studio export supported
- Kotlin support (minApi26)

## Tips & Tricks
- Always add Linear(V) first, set match_parent
- Use gravity for centering
- AppCompat needed for Material components
- Custom View = separate XML layout for list items
- FAB for floating actions (with Image Manager icons)
- Image icons from Material Icons (WHITE/BLACK sections)
- Use GONE vs INVISIBLE (GONE removes space)
- SharedPreferences file name must match across activities
- Position 0 = first item in lists (computer counting)
- Enable AppCompat before using MaterialButton/BottomNavigation
- onBindCustomView for ListView item rendering
- More Blocks = activity-level functions
- Custom Blocks = global reusable blocks via Block Manager
- Full docs clone: sketchware-docs.vercel.app/docs/getting-started.html

## GitHub Repository Structure
- **sketchware-pro/Sketchware-Pro** — main app (Java, open-source)
- **sketchware-pro/Sketchware-Pro-website** — docs website source
- **sketchware-pro/Sketchware-Pro-Docs** — documentation content
- **sketchware.pro** — official website
- **docs.sketchware.pro** — documentation website

## Known Limitations
- Course incomplete (as of Feb 2025, being written)
- Block manager requires Developer Tools
- SharedPreferences data lost on app uninstall
- Kotlin needs Android 8+ device
- No eval/exec (sandboxed, not clearly stated but implied)
- Custom blocks scope = global but need import
