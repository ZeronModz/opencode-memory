# Telebot Creator (TBC) — Knowledge Summary

**Platform:** telebotcreator.com | **Docs:** help.telebotcreator.com
**Platform v7.1.2** | **Telegram Bot API 10.1**

## Platform Overview
- Free Telegram bot hosting platform
- 80,000+ active bots, 150,000+ total created, 20M+ users served
- 100,000 free execution points/month per account (1 point = 1 command execution)
- TPY (Telebot Python) scripting language — Python-based, sandboxed

## Quick Start
1. Register at telebotcreator.com/register
2. Get bot token from @BotFather on Telegram
3. Add bot via dashboard + paste token
4. Write commands in TPY code editor
5. Start bot — live instantly

## TPY Language (Telebot Python)
- Python-based syntax (variables, if/else, loops, functions, try/except)
- 30+ built-in libraries — no installation needed
- Pre-defined globals: msg, message, bot, Bot, User, Account, params, u, options
- Sandboxed — no eval/exec, no os/sys/subprocess
- Up to 160 sec execution per command
- Allowed built-ins: str, int, float, bool, dict, list, len, range, sum, etc.

## Key Globals
| Global | Description |
|--------|-------------|
| msg | Raw text of incoming message |
| message | Full Telegram update object |
| bot | Low-level bot object (Telegram API methods) |
| Bot | High-level bot object (broadcast, data, scheduling) |
| User | User-specific data storage |
| Account | Cross-bot account-level data |
| params | Command parameters (e.g., /start ref_id) |
| u | Current user ID |
| left_points | Remaining points this month |
| options | Data passed to commands (webhook, etc.) |
| libs | Access to all libraries |
| HTTP | Custom HTTP client for API requests |
| time | Time utilities, delays, timestamps |
| regex/re | Pattern matching |
| base64, binascii, hashlib | Encoding/hashing |
| CSV | CSV file management |

## Bot Class (High-level) Key Methods
- `Bot.sendMessage(text)` — Send text
- `Bot.sendPhoto(photo, caption)` — Send photo
- `Bot.handleNextCommand(command, options)` — Wait for user input, route to command
- `Bot.runCommand(command, options)` — Immediately execute another command
- `Bot.runCommandAfter(timeout, command, options)` — Schedule command (1s to 366 days)
- `Bot.cancelScheduledTask(id)` — Cancel scheduled task
- `Bot.saveData(name, data)` — Save global bot data
- `Bot.getData(name)` — Retrieve global bot data
- `Bot.deleteData(name)` — Delete global bot data
- `Bot.broadcast(command/code, ...)` — Send to all users
- `Bot.genId()` / `Bot.genRandomId()` — Generate IDs
- `Bot.Transfer(email, bot_id, run_now)` — Transfer bot ownership
- `Bot.getIpnUrl(command)` — Get IPN URL for payments
- `Bot.getBotUsersFile()` — Export user list

## bot Class (Low-level) Key Methods
- `bot.sendMessage(chat_id, text, ...)` — Core message method
- `bot.answerCallbackQuery(id, text, show_alert)` — Callback responses
- `bot.answerInlineQuery(id, results)` — Inline query results
- `bot.editMessageText(text, ...)` — Edit sent messages
- `bot.banChatMember(chat_id, user_id)` — Ban user
- `bot.unbanChatMember(chat_id, user_id)` — Unban user
- `bot.exportChatInviteLink(chat_id)` — Get invite link
- All camelCase methods work with snake_case aliases too

## Commands
- Predefined triggers like /start, /help
- **Wildcard (*)** — catches unmatched input (fallback)
- **At Handler (@)** — runs before every command (preprocessing)
- `params` — access command parameters (/start ref_id → params = "ref_id")
- `update_type` — detect update type ("message", "callback_query", etc.)

## User Data Storage
- `User.saveData(name, data)` — Save per-user data
- `User.getData(name)` — Get per-user data
- `User.deleteData(name)` — Delete per-user data
- `User.getAllData()` — Get all data keys for user
- `User.getUserDataAsFile(name)` — Export as file

## Account Class (Cross-Bot Data)
- `Account.saveData(name, data)` — Save data accessible from all your bots
- `Account.getData(name)` — Retrieve cross-bot data
- `Account.deleteData(name)` — Delete cross-bot data
- `Account.getStats(bot_id, time_frame)` — Get user statistics
- `Account.TransferData(from_bot, to_bot, name)` — Transfer data between bots
- `Account.getBotUsersFile(bot_id)` — Export users from any bot

## Libraries (30+)
### AI
- `libs.openai_lib` — GPT-4o, Assistants API
- `libs.gemini_lib` — Gemini Flash/Pro
- OpenRouter — 100+ models via HTTP

### Payments
- `libs.Coinbase` — Coinbase Commerce
- `libs.Coinpayments` — CoinPayments
- `libs.Oxapay` — Oxapay
- `libs.MDxchange` — MDxchange

### Blockchain
- `libs.TonLib` — TON blockchain (wallet connect, transfers, NFTs, jetton)
- `libs.web3lib` — All EVM chains (transfer, balance, deploy)
- `libs.Crypto` — Crypto utilities
- `libs.Polygon` — Polygon-specific

### Data
- `libs.CSV` — CSV file management (create, read, write, delete)
- `libs.Resources` — Points/credits/leaderboard system (user, global, account level)

### Media
- `libs.OpenCV` — Image processing
- `libs.Pillow` — Image manipulation

### HTTP & Webhooks
- `libs.customHTTP` — Custom HTTP client
- `libs.Webhook` — Webhook management

### Utilities
- `libs.Random` — Random values
- `libs.DateAndTime` — Date/time utilities
- `libs.security` — Cryptographic toolkit (signing, encryption, hashing) — Added in v7.1.2

## Payment Features
- Coinbase Commerce: create charges, get IPN callbacks
- TON: wallet connect, TON transfers, NFT transfers, Jetton transfers
- Web3/EVMs: native coin and token transfers, balance checks, deploy contracts
- Stars (Telegram): check balance via `bot.getStarsAmount()`

## Broadcasting
- `Bot.broadcast(command="cmd")` — Run command for all users
- `Bot.broadcast(code="code")` — Execute code for all users
- Placeholders: `{user_id}`, `{first_name}`, `{username}` in code
- Broadcast V2: faster, resumable, speed-controllable
- `Bot.getBroadcastStatus(id)` — Check progress
- `Bot.stopBroadcast(id)` — Stop broadcast
- `Bot.clearBroadcast(id)` — Clear records

## Webhooks
- Real-time external service integration
- Set callback URL per command via `Bot.getIpnUrl(command)`
- Webhook data arrives via `options` in the target command
- V7.1.2: Access webhook request headers (`options["headers"]`) and client IP (`options["ip"]`)
- Whitelist IPs for security

## Scheduling
- `Bot.runCommandAfter(seconds, command)` — Schedule command
- Range: 1 second to 366 days (60*60*24*366)
- Returns `{"id": job_id, "command": cmd, "timeout": secs}`
- Cancel via `Bot.cancelScheduledTask(id)`
- Limits: 60/min per user, 50,000 outstanding per user

## Bot Management
- Transfer: `Bot.Transfer(email, bot_id, run_now)`
- Recovery: Deleted bots recoverable within 90 days
- Export/Import: Full bot export (json/yaml/txt), import commands only
- Cloning: Clone bot commands into new bot
- Account class methods for cross-bot management

## Special Update Types
TPY handles via `update_type` variable:
- message, edited_message, channel_post, edited_channel_post
- inline_query, chosen_inline_result, callback_query
- shipping_query, pre_checkout_query
- poll, poll_answer, chat_member, my_chat_member
- chat_join_request, chat_boost, removed_chat_boost
- message_reaction, message_reaction_count

Telegram Bot API 10.1 additions:
- Gifts (send, receive, transfer)
- Stories (post, hide, react)
- Business accounts (manage, message, working hours)
- Checklists (create, update, close)
- Suggested posts (moderate)
- Verification (status, customization)
- Stars balance & subscriptions
- Reaction deletion

## Points & Limits
- 100,000 points/month free (renewed monthly)
- 1 point per command execution
- Extra points: request free from admins
- Ad policy: 2-4 broadcast messages/month (minimal)
- Max execution time: 160 seconds (v5.0.0)
- Scheduled tasks: 50,000 max per user

## Full Documentation
Saved at: learned/2026-07-28-telebotcreator-full-docs.md (8685 lines, ~404KB)
