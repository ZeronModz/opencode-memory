# 🤖 Learned — 2026-08-03 — OpenRouter Free Models

## 1. nvidia/nemotron-3.5-content-safety:free is a CLASSIFIER, not chat model
- Sends anything → responds like `"User Safety: safe"` (with reasoning). CANNOT produce JSON/analysis.
- User had requested this model for AI analysis → engine must fall back.

## 2. Verified working FREE OpenRouter models (as of 2026-08-03)
- `nvidia/nemotron-3-super-120b-a12b:free` ✅ returns clean JSON (LLM analysis)
- `nvidia/nemotron-3-nano-30b-a3b:free` ✅ JSON
- `openrouter/free` ✅ (router to best free)
- `google/gemma-4-31b-it:free` ❌ empty content
- `meta-llama/llama-3.3-70b-instruct:free`, `deepseek/deepseek-chat-v3-0324:free`, `google/gemini-2.0-flash-exp:free`, `qwen/qwen-2.5-72b-instruct:free` ❌ 404 (not offered)
- User's first key: `sk-or-v1-d4bf3b2e46b168...` (from prompt). Second key used: `sk-or-v1-2792c97c967e...`.

## 3. Pattern: "user-requested model + verified fallback chain"
- Try requested model first. Detect non-JSON / "User Safety" / classifier output → skip to next model in fallback list. Cap attempts, graceful degrade.
- Design lesson: always probe `GET /openrouter.ai/api/v1/models` to discover real model IDs.

## 4. Other notes
- cheerio v1: named export `load`, NOT default export (`import { load } from "cheerio"`).
- Telegram bot file upload: native `FormData` + `Blob([buffer])` in Node 18+ — no SDK needed.
- Vercel CLI broken on this Termux (`bad interpreter: /usr/bin/env`) → deploy via GitHub import instead.
- Multi-page deep clone: pick pages excluding `pagesSeen`, extensionless page URLs → `dir/index.html` via `pageZipPath()`.
