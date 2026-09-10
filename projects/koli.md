# Koli - Project State

## Overview
- **Package**: `dev.zeron.koli`
- **App Name**: Koli
- **Base**: SwiftSlate (Musheer360/SwiftSlate) - Android Accessibility Service AI Text Assistant
- **Status**: Initial redesign completed (2026-09-10)

## What Was Done
1. **Package Rename**: `com.musheer360.swiftslate` → `dev.zeron.koli`
2. **App Name**: SwiftSlate → Koli (all 50+ locale strings.xml files)
3. **Icons**: Replaced with Koli_App_Icon_Pack (adaptive, all mipmap densities, playstore-icon)
4. **Dependencies**: Added OkHttp, kotlinx.serialization, Coil, DataStore, Navigation Compose
5. **Theme System**:
   - 12 Material 3 presets (4 imported from Material Theme Builder + 8 new curated)
   - ThemeBuilderEngine (seed color → full color scheme generation)
   - ThemeRepository (persistence, import/export via JSON)
   - WallpaperManager (photo picker, opacity slider)
6. **AI Providers**:
   - AiProvider interface with streaming support
   - GeminiProvider (Google's native API)
   - OpenAiCompatibleProviderBase (shared by Groq, OpenRouter, Cerebras, Mistral, Custom)
   - 6 total providers: Gemini, Groq, OpenRouter, Cerebras, Mistral, Custom
7. **UI Theme Components**:
   - Type.kt (Material 3 typography)
   - Shape.kt (Material 3 shapes)
   - Motion.kt (spring animations)
   - ThemeProvider.kt (dynamic color, AMOLED mode, wallpaper support)
8. **Themes Tab**: New tab with theme grid, custom theme builder, wallpaper section
9. **Bottom Navigation**: Updated to 5 tabs (Dashboard, Keys, Commands, Themes, Settings)

## File Structure
```
dev/zeron/koli/
├── ai/
│   ├── AiProvider.kt
│   └── providers/
│       ├── GeminiProvider.kt
│       ├── OpenAiCompatibleProviderBase.kt
│       ├── GroqProvider.kt
│       ├── OpenRouterProvider.kt
│       ├── CerebrasProvider.kt
│       ├── MistralProvider.kt
│       └── CustomProvider.kt
├── theme/
│   ├── ThemePresets.kt (12 presets)
│   ├── ThemeRepository.kt
│   ├── ThemeBuilderEngine.kt
│   └── WallpaperManager.kt
├── ui/
│   ├── ThemesScreen.kt (new)
│   └── theme/
│       ├── Type.kt
│       ├── Shape.kt
│       ├── Motion.kt
│       └── ThemeProvider.kt
└── model/
    └── ProviderType.kt (6 providers)
```

## Next Steps
- Build and test compilation
- Add custom fonts (Google Sans Flex, Roboto Flex, JetBrains Mono)
- Implement shared element transitions
- Add Lottie-style shimmer animation for processing indicator
- Complete custom theme builder bottom sheet
- Theme import/export with QR code support
