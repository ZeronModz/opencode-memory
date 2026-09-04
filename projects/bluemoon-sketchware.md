# BlueMoon (Sketchware-Pro Fork) Project

## Status: Active
## Location: /storage/emulated/0/AideProjects/Sketchware-Pro/
## Package: sketchware.bluemoon
## App Name: BlueMoon
## Version: v1.0.0-BlueMoon

## What It Is
Fork of Sketchware-Pro rebranded as "BlueMoon" with AI assistant feature.

## AI Assistant Feature (Added 2026-09-04)
- Package: `sketchware.bluemoon.ai.*`
- Chat UI: AiChatBottomSheet (bottom sheet with RecyclerView)
- Streaming: OkHttp-based SSE streaming for real-time AI responses
- Auto-generate: Can generate full Android app from prompt
- Auto-fix: Build errors sent back to AI, code fixed, auto-retry up to 3x
- Providers: OpenRouter, Google Gemini, AiHub Mix, opencode zen
- Entry point: FAB in DesignActivity (sparkle icon, bottom-right)

## Files Created/Modified
### Java
- ai/model/AiProvider.java, ChatMessage.java, ChatSession.java, ProviderConfig.java
- ai/service/ApiClient.java, AiService.java
- ai/context/ProjectAnalyzer.java
- ai/prompts/SystemPromptBuilder.java
- ai/actions/CodeApplier.java, BuildExecutor.java, CodeFixer.java
- ai/ui/AiChatBottomSheet.java, AiChatAdapter.java, AiProviderSettingsDialog.java
- com/besome/sketch/design/DesignActivity.java (FAB handler added)
- sketchware/bluemoon/utility/Configs.java

### Layouts
- bottom_sheet_ai_chat.xml, item_ai_message_user.xml, item_ai_message_ai.xml
- dialog_ai_provider_settings.xml, design.xml (FAB added)

### Drawables
- bg_message_user.xml, bg_message_ai.xml, bg_input_field.xml
- bg_bottom_sheet.xml, bg_bottom_sheet_handle.xml
- ic_add.xml, ic_settings.xml, ic_send.xml, ic_ai_avatar.xml, ic_ai_sparkle.xml

## Build Config
- SDK: android-34, android-36
- Build tools: 34.0.0, 35.0.0, 36.1.0
- Gradle: 8.13
- AGP: 8.12.0
- Kotlin: 2.1.21
- AAPT2: /data/data/com.termux/files/usr/bin/aapt2

## APK Output
- `/storage/emulated/0/Download/BlueMoon-AI-v1.0.apk` (158MB debug)
