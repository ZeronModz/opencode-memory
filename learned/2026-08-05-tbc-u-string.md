# TBC/TPY: `u` is a STRING, not int
- Telebot Creator injects `u` (current user ID) as type `str` (confirmed in docs; TeleBot Studio ref lists `u | str`).
- `if u != 5271912123:` (int compare) is ALWAYS True → admin gets denied, everyone gets denied.
- ALWAYS compare via `str(u) == "5271912123"` or `str(u) != "5271912123"`.
- `message.chat.id` and `message.from_user.id` are ints (fine to compare as int).
- Also: `Bot.getData("blocked")` comma-list check should use `str(u) in list(...)`.
