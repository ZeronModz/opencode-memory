# Project: ZERON-MCP-Android

## Overview
ZERON MCP Android app — connects to a local Node.js MCP server running on port 8787.

## Current State (2026-09-05)
- Core app files created (ZeronApp.kt, MainActivity.kt, McpClient.kt, Navigation.kt)
- Placeholder screens (Chat, Dashboard, Settings)
- **McpService.kt created** — foreground service to run Node.js MCP server

## File Structure
```
app/src/main/java/com/zeron/mcp/
├── ZeronApp.kt
├── MainActivity.kt
├── data/
│   ├── McpClient.kt
│   └── ThemeManager.kt
├── service/
│   └── McpService.kt          ← NEW
├── ui/
│   ├── navigation/
│   │   └── Navigation.kt
│   ├── screens/
│   │   ├── ChatScreen.kt
│   │   ├── DashboardScreen.kt
│   │   └── SettingsScreen.kt
│   └── theme/
│       ├── Color.kt
│       ├── Shape.kt
│       ├── Theme.kt
│       └── Type.kt
```

## Key Details
- Node.js binary: `/data/data/com.termux/files/usr/bin/node`
- Build dir: `/data/data/com.termux/files/home/.zeron-mcp-build/dist/index.js`
- MCP port: 8787
- Notification channel: `zeron_mcp_service`
- Service actions: `ACTION_START`, `ACTION_STOP`
- McpClient connects to `http://127.0.0.1:8787/mcp`
