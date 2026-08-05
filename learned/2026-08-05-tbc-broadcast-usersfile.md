# TBC Native Broadcast + User File APIs
## Broadcast (Broadcast V2)
- Signature: `Bot.broadcast(code=None, command=None, function=None, mode="single", speed=8, bot_ids=None, bot_id=None, api_key=None, warnings=None, **kwargs)`
- Provide exactly ONE of code/command/function.
- Function mode: `Bot.broadcast(function="send_message", text="...")` — chat_id auto-set per recipient; only allowed functions list (send_message, send_photo, ...).
- Do NOT use f-strings for personalization — use literal placeholders: `{first_name}`, `{user_name}`, `{username}`, `{user_id}`, `{chat_id}` (plain string replace per recipient).
- `warnings=False` silences the test-run notice sent to the admin.
- Returns dict with `broadcast_id`; runs async in background.
- Limits: 3 running per bot, 5000 global. Lifecycle: getAllBroadcasts/clearBroadcast/stopBroadcast/getBroadcastStatus/getBroadcastProgress/setBroadcastSpeed/pause/resume/rerun/listBroadcasts.
- Common failure: wrong function name, command doesn't exist, or 3 broadcasts running.

## User list (native)
- NO in-bot API returns users as a list. Only `Bot.getBotUsersFile(output_format="json"|"csv", include_creation_date, include_last_active_date)` returns a FILE.
- Send it: `bot.sendDocument(chat_id=..., document=Bot.getBotUsersFile(...), caption=..., parse_mode="HTML")`.
- Dashboard "Manage → Users" is the native interactive UI.
- User data: User.saveData/getData (user="id" optional), User.getAllDataOfUser(user) → file, User.getAllData(name) → file.

## JSON in TPY
- `bf_json()` BOTH parses JSON strings AND stringifies dicts/lists (round-trip safe). `jsondumps` exists but bf_json is the platform-canonical pairing used by existing zeron bot.
- Never compare `u` (str) to ints — always `str(u) == "..."`.
