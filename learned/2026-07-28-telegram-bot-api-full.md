# Telegram Bot API 10.2 — Complete Reference
Source: https://core.telegram.org/bots/api  
Date: 2026-07-28  
Version: Bot API 10.2 (July 14, 2026)

---

## 1. BASICS

### Authorization
- Token: `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`
- Base URL: `https://api.telegram.org/bot<token>/METHOD_NAME`

### Making Requests
- HTTP: GET and POST
- Content types: URL query string, application/x-www-form-urlencoded, application/json (no file upload), multipart/form-data (file upload)
- All methods case-insensitive, all queries UTF-8

### Response Format
```json
{"ok": true, "result": {...}}
{"ok": false, "description": "...", "error_code": 400, "parameters": {...}}
```
Fields: `ok` (Boolean), `description` (String, optional), `result`, `error_code` (Integer), `parameters` (ResponseParameters)

### Local Bot API Server
- Source: https://github.com/tdlib/telegram-bot-api
- Benefits: No file size limit, upload up to 2000 MB, local file paths, HTTP webhook, any IP/port, max_webhook_connections up to 100000, absolute local file_path

---

## 2. GETTING UPDATES

Two mutually exclusive methods:
1. **getUpdates** (long polling)
2. **setWebhook** (outgoing webhook)

Updates stored max 24 hours.

### Update Object Fields
| Field | Type | Description |
|-------|------|-------------|
| update_id | Integer | Unique identifier |
| message | Message | Optional. New incoming message |
| edited_message | Message | Optional. Edited message |
| channel_post | Message | Optional. New channel post |
| edited_channel_post | Message | Optional. Edited channel post |
| business_connection | BusinessConnection | Optional. Business connection |
| business_message | Message | Optional. Business message |
| edited_business_message | Message | Optional. Edited business message |
| deleted_business_messages | BusinessMessagesDeleted | Optional. Deleted business messages |
| guest_message | Message | Optional. Guest message |
| message_reaction | MessageReactionUpdated | Optional. Reaction changed |
| message_reaction_count | MessageReactionCountUpdated | Optional. Reaction count changed |
| inline_query | InlineQuery | Optional. Inline query |
| chosen_inline_result | ChosenInlineResult | Optional. Chosen inline result |
| callback_query | CallbackQuery | Optional. Callback query |
| shipping_query | ShippingQuery | Optional. Shipping query |
| pre_checkout_query | PreCheckoutQuery | Optional. Pre-checkout query |
| purchased_paid_media | PaidMediaPurchased | Optional. Paid media purchased |
| poll | Poll | Optional. Poll state |
| poll_answer | PollAnswer | Optional. Poll answer changed |
| my_chat_member | ChatMemberUpdated | Optional. Bot's chat member status |
| chat_member | ChatMemberUpdated | Optional. Chat member status |
| chat_join_request | ChatJoinRequest | Optional. Join request |
| chat_boost | ChatBoostUpdated | Optional. Chat boost added/changed |
| removed_chat_boost | ChatBoostRemoved | Optional. Boost removed |
| managed_bot | ManagedBotUpdated | Optional. Managed bot changed |
| subscription | BotSubscriptionUpdated | Optional. Subscription changed |

### getUpdates Parameters
- `offset` (Integer, opt) — first update ID
- `limit` (Integer, opt, 1-100, default 100)
- `timeout` (Integer, opt, long polling seconds)
- `allowed_updates` (Array of String, opt) — update types to receive

### setWebhook Parameters
- `url` (String, req) — HTTPS URL
- `certificate` (InputFile, opt) — public key certificate
- `ip_address` (String, opt) — fixed IP
- `max_connections` (Integer, opt, 1-100, default 40)
- `allowed_updates` (Array of String, opt)
- `drop_pending_updates` (Boolean, opt)
- `secret_token` (String, opt, 1-256 chars, A-Z a-z 0-9 _ -)

### deleteWebhook
- `drop_pending_updates` (Boolean, opt)

### getWebhookInfo
Returns WebhookInfo (url, has_custom_certificate, pending_update_count, ip_address, last_error_date, last_error_message, last_synchronization_error_date, max_connections, allowed_updates)

---

## 3. AVAILABLE TYPES

### User
id (Integer), is_bot (Boolean), first_name (String), last_name (String, opt), username (String, opt), language_code (String, opt), is_premium (True, opt), added_to_attachment_menu (True, opt), can_join_groups (Boolean, opt), can_read_all_group_messages (Boolean, opt), supports_guest_queries (Boolean, opt), supports_inline_queries (Boolean, opt), can_connect_to_business (Boolean, opt), has_main_web_app (Boolean, opt), has_topics_enabled (Boolean, opt), allows_users_to_create_topics (Boolean, opt), can_manage_bots (Boolean, opt), supports_join_request_queries (Boolean, opt)

### Chat
id (Integer), type (String: "private", "group", "supergroup", "channel"), title (String, opt), username (String, opt), first_name (String, opt), last_name (String, opt), is_forum (True, opt), is_direct_messages (True, opt)

### ChatFullInfo
Same as Chat + accent_color_id, max_reaction_count, photo (ChatPhoto), active_usernames, birthdate, business_intro, business_location, business_opening_hours, personal_chat, parent_chat, available_reactions, background_custom_emoji_id, profile_accent_color_id, profile_background_custom_emoji_id, emoji_status_custom_emoji_id, emoji_status_expiration_date, bio, has_private_forwards, has_restricted_voice_and_video_messages, join_to_send_messages, join_by_request, description, invite_link, pinned_message, permissions, accepted_gift_types, can_send_paid_media, slow_mode_delay, unrestrict_boost_count, message_auto_delete_time, has_aggressive_anti_spam_enabled, has_hidden_members, has_protected_content, has_visible_history, sticker_set_name, can_set_sticker_set, custom_emoji_sticker_set_name, linked_chat_id, location, rating, first_profile_audio, unique_gift_colors, paid_message_star_count, guard_bot, community

### Message (massive object)
message_id (Integer), message_thread_id (Integer, opt), direct_messages_topic (DirectMessagesTopic, opt), from (User, opt), sender_chat (Chat, opt), sender_boost_count (Integer, opt), sender_business_bot (User, opt), sender_tag (String, opt), receiver_user (User, opt), ephemeral_message_id (Integer, opt), date (Integer), guest_query_id (String, opt), business_connection_id (String, opt), chat (Chat), forward_origin (MessageOrigin, opt), is_topic_message (True, opt), is_automatic_forward (True, opt), reply_to_message (Message, opt), external_reply (ExternalReplyInfo, opt), quote (TextQuote, opt), reply_to_story (Story, opt), reply_to_checklist_task_id (Integer, opt), reply_to_poll_option_id (String, opt), via_bot (User, opt), guest_bot_caller_user (User, opt), guest_bot_caller_chat (Chat, opt), edit_date (Integer, opt), has_protected_content (True, opt), is_from_offline (True, opt), is_paid_post (True, opt), media_group_id (String, opt), author_signature (String, opt), paid_star_count (Integer, opt), text (String, opt), entities (Array of MessageEntity, opt), link_preview_options (LinkPreviewOptions, opt), suggested_post_info (SuggestedPostInfo, opt), effect_id (String, opt), rich_message (RichMessage, opt), animation (Animation, opt), audio (Audio, opt), document (Document, opt), live_photo (LivePhoto, opt), paid_media (PaidMediaInfo, opt), photo (Array of PhotoSize, opt), sticker (Sticker, opt), story (Story, opt), video (Video, opt), video_note (VideoNote, opt), voice (Voice, opt), caption (String, opt), caption_entities (Array of MessageEntity, opt), show_caption_above_media (True, opt), has_media_spoiler (True, opt), checklist (Checklist, opt), contact (Contact, opt), dice (Dice, opt), game (Game, opt), poll (Poll, opt), venue (Venue, opt), location (Location, opt), new_chat_members (Array of User, opt), left_chat_member (User, opt), chat_owner_left, chat_owner_changed, new_chat_title, new_chat_photo, delete_chat_photo, group_chat_created, supergroup_chat_created, channel_chat_created, message_auto_delete_timer_changed, migrate_to_chat_id, migrate_from_chat_id, pinned_message (MaybeInaccessibleMessage), invoice, successful_payment, refunded_payment, users_shared, chat_shared, gift (GiftInfo), unique_gift (UniqueGiftInfo), gift_upgrade_sent, connected_website, write_access_allowed, passport_data, proximity_alert_triggered, boost_added, chat_background_set, checklist_tasks_done, checklist_tasks_added, community_chat_added, community_chat_removed, direct_message_price_changed, forum_topic_created/edited/closed/reopened, general_forum_topic_hidden/unhidden, giveaway_created/giveaway/giveaway_winners/giveaway_completed, managed_bot_created, paid_message_price_changed, poll_option_added/deleted, suggested_post_approved/approval_failed/declined/paid/refunded, video_chat_scheduled/started/ended/participants_invited, web_app_data, reply_markup

### MessageEntity
type (String), offset (Integer), length (Integer), url (String, opt), user (User, opt), language (String, opt), custom_emoji_id (String, opt), unix_time (Integer, opt), date_time_format (String, opt)

Types: "mention", "hashtag", "cashtag", "bot_command", "url", "email", "phone_number", "bank_card", "bold", "italic", "underline", "strikethrough", "spoiler", "code", "pre", "text_link", "text_mention", "custom_emoji", "blockquote", "pullquote", "expandable_blockquote", "subscript", "superscript", "marked", "date_time", "mathematical_expression"

### TextQuote
text (String), entities (Array of MessageEntity, opt), position (Integer), is_manual (True, opt)

### ExternalReplyInfo
origin (MessageOrigin), chat (Chat, opt), message_id (Integer, opt), link_preview_options (LinkPreviewOptions, opt), animation (Animation, opt), audio (Audio, opt), document (Document, opt), live_photo (LivePhoto, opt), paid_media (PaidMediaInfo, opt), photo (Array of PhotoSize, opt), sticker (Sticker, opt), story (Story, opt), video (Video, opt), video_note (VideoNote, opt), voice (Voice, opt), caption (String, opt), caption_entities (Array of MessageEntity, opt), show_caption_above_media (True, opt), has_media_spoiler (True, opt), checklist (Checklist, opt), contact (Contact, opt), dice (Dice, opt), game (Game, opt), poll (Poll, opt), venue (Venue, opt), location (Location, opt), invoice (Invoice, opt), successful_payment (SuccessfulPayment, opt), gift (GiftInfo, opt), unique_gift (UniqueGiftInfo, opt), passport_data (PassportData, opt)

### ReplyParameters
message_id (Integer, opt), chat_id (Integer/String, opt), ephemeral_message_id (Integer, opt), allow_sending_without_reply (Boolean, opt), quote (String, opt), quote_parse_mode (String, opt), quote_entities (Array of MessageEntity, opt), quote_position (Integer, opt), checklist_task_id (Integer, opt), poll_option_id (String, opt)

### MessageOrigin (union)
- MessageOriginUser: type="user", date, sender_user
- MessageOriginHiddenUser: type="hidden_user", date, sender_user_name
- MessageOriginChat: type="chat", date, sender_chat, author_signature (opt)
- MessageOriginChannel: type="channel", date, chat, message_id, author_signature (opt)

### PhotoSize
file_id, file_unique_id, width, height, file_size (opt)

### Animation
file_id, file_unique_id, width, height, duration, thumbnail (opt), file_name (opt), mime_type (opt), file_size (opt)

### Audio
file_id, file_unique_id, duration, performer (opt), title (opt), file_name (opt), mime_type (opt), file_size (opt), thumbnail (opt)

### Document
file_id, file_unique_id, thumbnail (opt), file_name (opt), mime_type (opt), file_size (opt)

### LivePhoto
photo (Array of PhotoSize, opt), file_id, file_unique_id, width, height, duration, mime_type (opt), file_size (opt)

### Story
chat (Chat), id (Integer)

### Video
file_id, file_unique_id, width, height, duration, thumbnail (opt), cover (Array of PhotoSize, opt), start_timestamp (Integer, opt), qualities (Array of VideoQuality, opt), file_name (opt), mime_type (opt), file_size (opt)

### VideoNote
file_id, file_unique_id, length, duration, thumbnail (opt), file_size (opt)

### Voice
file_id, file_unique_id, duration, mime_type (opt), file_size (opt)

### PaidMediaInfo
star_count (Integer), paid_media (Array of PaidMedia)

### PaidMedia (union)
- PaidMediaPreview: type="preview", width, height, duration
- PaidMediaPhoto: type="photo", photo
- PaidMediaVideo: type="video", video
- PaidMediaLivePhoto: type="live_photo", live_photo

### Contact
phone_number, first_name, last_name (opt), user_id (opt), vcard (opt)

### Dice
emoji (String), value (Integer, 1-6 / 1-5 / 1-64)

### PollOption
persistent_id, text, text_entities (opt), media (PollMedia, opt), voter_count, added_by_user (opt), added_by_chat (opt), addition_date (opt)

### InputPollOption
text, text_parse_mode (opt), text_entities (opt), media (InputPollOptionMedia, opt)

### PollAnswer
poll_id, voter_chat (opt), user (opt), option_ids (Array of Integer), option_persistent_ids (Array of String)

### Poll
id, question, question_entities (opt), options, total_voter_count, is_closed, is_anonymous, type ("regular"/"quiz"), allows_multiple_answers, allows_revoting, members_only (opt), country_codes (opt), correct_option_ids (opt), explanation (opt), explanation_entities (opt), explanation_media (PollMedia, opt), open_period (opt), close_date (opt), description (opt), description_entities (opt), media (PollMedia, opt)

### Link
url (String)

### PollMedia (union: Animation/Audio/Document/Link/LivePhoto/Location/Photo/Sticker/Venue/Video)

### ChecklistTask
id, text, text_entities (opt), completed_by_user (opt), completed_by_chat (opt), completion_date (opt)

### Checklist
title, title_entities (opt), tasks, others_can_add_tasks (opt), others_can_mark_tasks_as_done (opt)

### InputChecklist
title, parse_mode (opt), title_entities (opt), tasks (1-30), others_can_add_tasks, others_can_mark_tasks_as_done

### Location
latitude, longitude, horizontal_accuracy (opt, 0-1500m), live_period (opt), heading (opt, 1-360), proximity_alert_radius (opt)

### Venue
location, title, address, foursquare_id (opt), foursquare_type (opt), google_place_id (opt), google_place_type (opt)

### WebAppData
data, button_text

### ProximityAlertTriggered
traveler, watcher, distance

### MessageAutoDeleteTimerChanged
message_auto_delete_time

### ChatBoostAdded
boost_count

### BackgroundFill (union: Solid/Gradient/FreeformGradient)

### BackgroundType (union: Fill/Wallpaper/Pattern/ChatTheme)

### ChatBackground
type (BackgroundType)

### ForumTopic
message_thread_id, name, icon_color, icon_custom_emoji_id (opt), is_name_implicit (opt)

### SharedUser
user_id, first_name (opt), last_name (opt), username (opt), photo (opt)

### UsersShared
request_id, users

### ChatShared
request_id, chat_id, title (opt), username (opt), photo (opt)

### WriteAccessAllowed
from_request (opt), web_app_name (opt), from_attachment_menu (opt)

### VideoChatScheduled/Started/Ended/ParticipantsInvited

### LinkPreviewOptions
is_disabled (opt), url (opt), prefer_small_media (opt), prefer_large_media (opt), show_above_text (opt)

### SuggestedPostInfo
state ("pending"/"approved"/"declined"), price (opt), send_date (opt)

### SuggestedPostParameters
price (opt), send_date (opt, 300s-2678400s future)

### DirectMessagesTopic
topic_id, user (opt)

### UserProfilePhotos
total_count, photos (Array of Array of PhotoSize)

### UserProfileAudios
total_count, audios (Array of Audio)

### File
file_id, file_unique_id, file_size (opt), file_path (opt)

### WebAppInfo
url

### ReplyKeyboardMarkup
keyboard (Array of Array of KeyboardButton), is_persistent (opt), resize_keyboard (opt), one_time_keyboard (opt), input_field_placeholder (opt, 1-64), selective (opt)

### KeyboardButton
text, icon_custom_emoji_id (opt), style ("danger"/"success"/"primary", opt), request_users, request_chat, request_managed_bot, request_contact, request_location, request_poll, web_app

### KeyboardButtonRequestUsers
request_id, user_is_bot, user_is_premium, max_quantity (1-10), request_name, request_username, request_photo

### KeyboardButtonRequestChat
request_id, chat_is_channel, chat_is_forum, chat_has_username, chat_is_created, user_administrator_rights, bot_administrator_rights, bot_is_member, request_title, request_username, request_photo

### KeyboardButtonRequestManagedBot
request_id, suggested_name, suggested_username

### KeyboardButtonPollType
type ("quiz"/"regular")

### ReplyKeyboardRemove
remove_keyboard, selective (opt)

### InlineKeyboardMarkup
inline_keyboard (Array of Array of InlineKeyboardButton)

### InlineKeyboardButton
text, icon_custom_emoji_id (opt), style ("danger"/"success"/"primary", opt), url, callback_data, web_app, login_url, switch_inline_query, switch_inline_query_current_chat, switch_inline_query_chosen_chat, copy_text, callback_game, pay

### LoginUrl
url, forward_text, bot_username, request_write_access

### SwitchInlineQueryChosenChat
query, allow_user_chats, allow_bot_chats, allow_group_chats, allow_channel_chats

### CopyTextButton
text (1-256)

### CallbackQuery
id, from, message (MaybeInaccessibleMessage, opt), inline_message_id (opt), chat_instance, data (opt), game_short_name (opt)

### ForceReply
force_reply, input_field_placeholder (opt, 1-64), selective (opt)

### Community
id, name

### ChatPhoto
small_file_id, small_file_unique_id, big_file_id, big_file_unique_id

### ChatInviteLink
invite_link, creator, creates_join_request, is_primary, is_revoked, name (opt), expire_date (opt), member_limit (opt, 1-99999), pending_join_request_count (opt), subscription_period (opt), subscription_price (opt)

### ChatAdministratorRights
is_anonymous, can_manage_chat, can_delete_messages, can_manage_video_chats, can_restrict_members, can_promote_members, can_change_info, can_invite_users, can_post_stories, can_edit_stories, can_delete_stories, can_post_messages (channels, opt), can_edit_messages (channels, opt), can_pin_messages (groups, opt), can_manage_topics (supergroups, opt), can_manage_direct_messages (channels, opt), can_manage_tags (groups, opt)

### ChatMemberUpdated
chat, from, date, old_chat_member, new_chat_member, invite_link (opt), via_join_request (opt), via_chat_folder_invite_link (opt)

### ChatMember (union: Owner/Administrator/Member/Restricted/Left/Banned)

### ChatPermissions
can_send_messages, can_send_audios, can_send_documents, can_send_photos, can_send_videos, can_send_video_notes, can_send_voice_notes, can_send_polls, can_send_other_messages, can_add_web_page_previews, can_react_to_messages, can_edit_tag, can_change_info, can_invite_users, can_pin_messages, can_manage_topics (all Boolean opt)

### Birthdate
day, month, year (opt)

### BusinessIntro/BusinessLocation/BusinessOpeningHours

### ChatLocation
location, address

### ReactionType (union: Emoji/CustomEmoji/Paid)

### ReactionCount
type, total_count

### MessageReactionUpdated/MessageReactionCountUpdated

### ChatBoost
boost_id, add_date, expiration_date, source (ChatBoostSource)

### ChatBoostSource (union: Premium/GiftCode/Giveaway)

### BotCommand
command (1-32), description (1-256), is_ephemeral (opt)

### BotCommandScope (union: Default/AllPrivateChats/AllGroupChats/AllChatAdministrators/Chat/ChatAdministrators/ChatMember)

### BotName/BotDescription/BotShortDescription

### MenuButton (union: Commands/WebApp/Default)

### ResponseParameters
migrate_to_chat_id (opt), retry_after (opt)

### InputMedia (union: Animation/Audio/Document/LivePhoto/Photo/Video)

### InputPaidMedia (union: PaidMediaLivePhoto/PaidMediaPhoto/PaidMediaVideo)

### InputProfilePhoto (union: Static/Animated)

### InputStoryContent (union: Photo/Video)

### InputMediaLink
type="link", url

### InputMediaVoiceNote
type="voice_note", media, caption, parse_mode, caption_entities, duration

### InputRichMessageMedia
id, media (InputMediaAnimation/Audio/Photo/Video/VoiceNote)

---

## 4. RICH MESSAGE SYSTEM (API 10.1 + 10.2)

### RichText (union of 25 types)
- RichTextBold: type="bold", text:RichText
- RichTextItalic: type="italic", text:RichText
- RichTextUnderline: type="underline", text:RichText
- RichTextStrikethrough: type="strikethrough", text:RichText
- RichTextSpoiler: type="spoiler", text:RichText
- RichTextDateTime: type="date_time", text, unix_time, date_time_format
- RichTextTextMention: type="text_mention", text, user
- RichTextSubscript: type="subscript", text
- RichTextSuperscript: type="superscript", text
- RichTextMarked: type="marked", text
- RichTextCode: type="code", text
- RichTextCustomEmoji: type="custom_emoji", custom_emoji_id, alternative_text
- RichTextMathematicalExpression: type="mathematical_expression", expression
- RichTextUrl: type="url", text, url
- RichTextEmailAddress: type="email_address", text, email_address
- RichTextPhoneNumber: type="phone_number", text, phone_number
- RichTextBankCardNumber: type="bank_card_number", text, bank_card_number
- RichTextMention: type="mention", text, username
- RichTextHashtag: type="hashtag", text, hashtag
- RichTextCashtag: type="cashtag", text, cashtag
- RichTextBotCommand: type="bot_command", text, bot_command
- RichTextAnchor: type="anchor", name
- RichTextAnchorLink: type="anchor_link", text, anchor_name
- RichTextReference: type="reference", text, name
- RichTextReferenceLink: type="reference_link", text, reference_name

### RichMessage
blocks (Array of RichBlock), is_rtl (Boolean, opt)

### RichBlock (union of 17 types)
- RichBlockParagraph: type="paragraph", text:RichText
- RichBlockSectionHeading: type="heading", text:RichText, size (1-6)
- RichBlockPreformatted: type="pre", text:RichText, language (opt)
- RichBlockFooter: type="footer", text:RichText
- RichBlockDivider: type="divider"
- RichBlockMathematicalExpression: type="mathematical_expression", expression
- RichBlockAnchor: type="anchor", name
- RichBlockList: type="list", items (Array of RichBlockListItem)
- RichBlockBlockQuotation: type="blockquote", blocks, credit (opt)
- RichBlockPullQuotation: type="pullquote", text, credit (opt)
- RichBlockCollage: type="collage", blocks, caption (opt)
- RichBlockSlideshow: type="slideshow", blocks, caption (opt)
- RichBlockTable: type="table", cells, is_bordered, is_striped, caption (opt)
- RichBlockDetails: type="details", summary, blocks, is_open (opt)
- RichBlockMap: type="map", location, zoom (13-20), width, height, caption (opt)
- RichBlockAnimation: type="animation", animation, has_spoiler (opt), caption (opt)
- RichBlockAudio: type="audio", audio, caption (opt)
- RichBlockPhoto: type="photo", photo, has_spoiler (opt), caption (opt)
- RichBlockVideo: type="video", video, has_spoiler (opt), caption (opt)
- RichBlockVoiceNote: type="voice_note", voice_note, caption (opt)
- RichBlockThinking: type="thinking", text:RichText (only in Draft)

### InputRichMessage
blocks (Array of InputRichBlock, opt), html (String, opt), markdown (String, opt), media (Array of InputRichMessageMedia, opt), is_rtl (Boolean, opt), skip_entity_detection (Boolean, opt)

### InputRichBlock (mirrors RichBlock, media fields use InputMedia types)

### RichBlockCaption
text (RichText), credit (RichText, opt)

### RichBlockTableCell
text (RichText, opt), is_header (True, opt), colspan (opt), rowspan (opt), align ("left"/"center"/"right", opt), valign ("top"/"middle"/"bottom", opt)

### RichBlockListItem
label, blocks, has_checkbox (opt), is_checked (opt), value (opt), type ("a"/"A"/"i"/"I"/"1", opt)

---

## 5. ALL AVAILABLE METHODS

### Core
| Method | Parameters | Returns |
|--------|-----------|---------|
| getMe | — | User |
| logOut | — | True |
| close | — | True |

### Messages
| Method | Key Params | Returns |
|--------|-----------|---------|
| sendMessage | chat_id*, text*, parse_mode, entities, link_preview_options, reply_parameters, reply_markup + common | Message |
| sendMessageDraft | chat_id*, draft_id*, text, parse_mode, entities | Message |
| sendRichMessage | chat_id*, rich_message*, reply_parameters, reply_markup + common | Message |
| sendRichMessageDraft | chat_id*, draft_id*, rich_message* | Message |
| forwardMessage | chat_id*, from_chat_id*, message_id* + common | Message |
| forwardMessages | chat_id*, from_chat_id*, message_ids* (1-100) + common | Array of MessageId |
| copyMessage | chat_id*, from_chat_id*, message_id* + common | MessageId |
| copyMessages | chat_id*, from_chat_id*, message_ids* (1-100) + common | Array of MessageId |
| sendPhoto | chat_id*, photo*, caption, has_spoiler + common | Message |
| sendLivePhoto | chat_id*, live_photo*, photo*, caption, has_spoiler + common | Message |
| sendAudio | chat_id*, audio*, caption, duration, performer, title, thumbnail + common | Message |
| sendDocument | chat_id*, document*, caption, thumbnail, disable_content_type_detection + common | Message |
| sendVideo | chat_id*, video*, caption, duration, width, height, thumbnail, cover, start_timestamp, supports_streaming, has_spoiler + common | Message |
| sendAnimation | chat_id*, animation*, caption, duration, width, height, thumbnail, has_spoiler + common | Message |
| sendVoice | chat_id*, voice*, caption, duration + common | Message |
| sendVideoNote | chat_id*, video_note*, duration, length, thumbnail + common | Message |
| sendPaidMedia | chat_id*, star_count* (1-25000), media*, payload, caption + common | Message |
| sendMediaGroup | chat_id*, media* (Array of InputMedia) + common | Array of Message |
| sendLocation | chat_id*, latitude*, longitude*, horizontal_accuracy, live_period, heading, proximity_alert_radius + common | Message |
| sendVenue | chat_id*, latitude*, longitude*, title*, address* + common | Message |
| sendContact | chat_id*, phone_number*, first_name*, last_name, vcard + common | Message |
| sendPoll | chat_id*, question*, options* + many poll params + common | Message |
| sendDice | chat_id*, emoji + common | Message |
| sendChecklist | business_connection_id*, chat_id*, checklist* + common | Message |
| sendChatAction | chat_id*, action* ("typing"/"upload_photo"/"record_video"/"upload_video"/"record_voice"/"upload_voice"/"upload_document"/"choose_sticker"/"find_location"/"record_video_note"/"upload_video_note") | True |
| setMessageReaction | chat_id*, message_id*, reaction (Array of ReactionType), is_big | True |

### Ephemeral Messages (API 10.2)
| Method | Key Params | Returns |
|--------|-----------|---------|
| editEphemeralMessageText | chat_id*, receiver_user_id*, ephemeral_message_id*, text*, parse_mode, entities, link_preview_options, reply_markup | Message |
| editEphemeralMessageMedia | chat_id*, receiver_user_id*, ephemeral_message_id*, media*, reply_markup | Message |
| editEphemeralMessageCaption | chat_id*, receiver_user_id*, ephemeral_message_id*, caption, parse_mode, caption_entities, reply_markup | Message |
| editEphemeralMessageReplyMarkup | chat_id*, receiver_user_id*, ephemeral_message_id*, reply_markup | Message |
| deleteEphemeralMessage | chat_id*, receiver_user_id*, ephemeral_message_id* | True |

Common params for send*: business_connection_id (opt), chat_id* (req), message_thread_id (opt), direct_messages_topic_id (opt), receiver_user_id (opt), callback_query_id (opt), disable_notification (opt), protect_content (opt), allow_paid_broadcast (opt), message_effect_id (opt), suggested_post_parameters (opt), reply_parameters (opt), reply_markup (opt)

### Edit/Delete Messages
| Method | Key Params | Returns |
|--------|-----------|---------|
| editMessageText | (chat_id+message_id or inline_message_id), text/rich_message, parse_mode, entities, link_preview_options, reply_markup | Message |
| editMessageCaption | (chat_id+message_id or inline_message_id), caption, parse_mode, caption_entities, show_caption_above_media, reply_markup | Message |
| editMessageMedia | (chat_id+message_id or inline_message_id), media*, reply_markup | Message |
| editMessageReplyMarkup | (chat_id+message_id or inline_message_id), reply_markup | Message |
| deleteMessage | chat_id*, message_id* | True |
| stopPoll | business_connection_id (opt), chat_id*, message_id*, reply_markup | Poll |
| deleteAllMessageReactions | chat_id*, user_id (opt), actor_chat_id (opt) | True |
| deleteMessageReaction | chat_id*, message_id*, user_id (opt), actor_chat_id (opt) | True |

### Stickers
| Method | Key Params | Returns |
|--------|-----------|---------|
| sendSticker | chat_id*, sticker*, emoji (opt) + common | Message |
| getStickerSet | name* | StickerSet |
| getCustomEmojiStickers | custom_emoji_ids* | Array of Sticker |
| uploadStickerFile | user_id*, sticker*, sticker_format* | File |
| createNewStickerSet | user_id*, name*, title*, stickers*, sticker_type, needs_repainting | True |
| addStickerToSet | user_id*, name*, sticker* | True |
| setStickerPositionInSet | sticker*, position* | True |
| deleteStickerFromSet | sticker* | True |
| replaceStickerInSet | user_id*, name*, old_sticker*, sticker* | True |
| setStickerEmojiList | sticker*, emoji_list* | True |
| setStickerKeywords | sticker*, keywords* | True |
| setStickerMaskPosition | sticker*, mask_position* | True |
| setStickerSetTitle | name*, title* | True |
| setStickerSetThumbnail | name*, user_id*, thumbnail*, format* | True |
| setCustomEmojiStickerSetThumbnail | name*, custom_emoji_id* | True |
| deleteStickerSet | name* | True |

### Gifts
| Method | Key Params | Returns |
|--------|-----------|---------|
| getAvailableGifts | — | Gifts |
| sendGift | user_id (opt), chat_id (opt), gift_id*, pay_for_upgrade, text, text_parse_mode, text_entities, etc. | True |
| giftPremiumSubscription | user_id*, month_count*, is_paid_for_by_receiver, paid_for_star_count, text, etc. | True |
| getBusinessAccountGifts | business_connection_id*, exclude_saved_gifts, exclude_unsaved_gifts, exclude_refunded_gifts, offset, limit | OwnedGifts |
| getUserGifts | user_id*, offset, limit | OwnedGifts |
| getChatGifts | chat_id*, offset, limit | OwnedGifts |
| convertGiftToStars | business_connection_id*, owned_gift_id* | True |
| upgradeGift | business_connection_id*, owned_gift_id*, keep_original_details, star_count | True |
| transferGift | business_connection_id*, owned_gift_id*, new_owner_chat_id*, star_count | True |

### Inline Mode
| Method | Key Params | Returns |
|--------|-----------|---------|
| answerInlineQuery | inline_query_id*, results*, cache_time, is_personal, next_offset, button | True |

### Payments / Telegram Stars
| Method | Key Params | Returns |
|--------|-----------|---------|
| sendInvoice | chat_id*, title*, description*, payload*, provider_token, currency*, prices*, max_tip_amount, suggested_tip_amounts, provider_data, photo_url, photo_size, photo_width, photo_height, need_name, need_phone_number, need_email, need_shipping_address, send_phone_number_to_provider, send_email_to_provider, is_flexible, subscription_period + common | Message |
| createInvoiceLink | title*, description*, payload*, provider_token, currency*, prices*, subscription_period + same as sendInvoice | String |
| answerShippingQuery | shipping_query_id*, ok*, shipping_options, error_message | True |
| answerPreCheckoutQuery | pre_checkout_query_id*, ok*, error_message | True |
| getMyStarBalance | — | StarAmount |
| getStarTransactions | offset, limit | StarTransactions |
| refundStarPayment | user_id*, telegram_payment_charge_id* | True |
| editUserStarSubscription | user_id*, telegram_payment_charge_id*, is_canceled* | True |

### Passport
| Method | Key Params | Returns |
|--------|-----------|---------|
| setPassportDataErrors | user_id*, errors* | True |

### Games
| Method | Key Params | Returns |
|--------|-----------|---------|
| sendGame | chat_id*, game_short_name* + common | Message |
| setGameScore | user_id*, score*, force, disable_edit_message, (chat_id+message_id or inline_message_id) | Message |
| getGameHighScores | user_id*, (chat_id+message_id or inline_message_id) | Array of GameHighScore |

### Chat Management
| Method | Key Params | Returns |
|--------|-----------|---------|
| getChat | chat_id* | ChatFullInfo |
| getChatFullInfo | — | (use getChat) |
| getChatAdministrators | chat_id*, return_bots (opt) | Array of ChatMember |
| getChatMemberCount | chat_id* | Integer |
| getChatMember | chat_id*, user_id* | ChatMember |
| banChatMember | chat_id*, user_id*, until_date, revoke_messages | True |
| unbanChatMember | chat_id*, user_id*, only_if_banned | True |
| restrictChatMember | chat_id*, user_id*, permissions*, use_independent_chat_permissions, until_date | True |
| promoteChatMember | chat_id*, user_id*, is_anonymous, can_manage_chat, can_delete_messages, can_manage_video_chats, can_restrict_members, can_promote_members, can_change_info, can_invite_users, can_post_stories, can_edit_stories, can_delete_stories, can_post_messages, can_edit_messages, can_pin_messages, can_manage_topics, can_manage_direct_messages, can_manage_tags | True |
| setChatAdministratorCustomTitle | chat_id*, user_id*, custom_title* | True |
| banChatSenderChat | chat_id*, sender_chat_id* | True |
| unbanChatSenderChat | chat_id*, sender_chat_id* | True |
| setChatPermissions | chat_id*, permissions*, use_independent_chat_permissions | True |
| exportChatInviteLink | chat_id* | String |
| createChatInviteLink | chat_id*, name, expire_date, member_limit, creates_join_request | ChatInviteLink |
| createChatSubscriptionInviteLink | chat_id*, subscription_period*, subscription_price*, name, is_primary, creates_join_request | ChatInviteLink |
| editChatInviteLink | chat_id*, invite_link*, name, expire_date, member_limit, creates_join_request | ChatInviteLink |
| editChatSubscriptionInviteLink | chat_id*, invite_link*, name, creates_join_request, is_primary | ChatInviteLink |
| revokeChatInviteLink | chat_id*, invite_link* | ChatInviteLink |
| approveChatJoinRequest | chat_id*, user_id* | True |
| declineChatJoinRequest | chat_id*, user_id* | True |
| answerChatJoinRequestQuery | chat_join_request_query_id*, result* | True |
| sendChatJoinRequestWebApp | chat_join_request_query_id*, web_app_url* | SentWebAppMessage |
| setChatPhoto | chat_id*, photo* | True |
| deleteChatPhoto | chat_id* | True |
| setChatTitle | chat_id*, title* | True |
| setChatDescription | chat_id*, description | True |
| setChatStickerSet | chat_id*, sticker_set_name* | True |
| deleteChatStickerSet | chat_id* | True |
| pinChatMessage | business_connection_id, chat_id*, message_id*, disable_notification | True |
| unpinChatMessage | business_connection_id, chat_id*, message_id | True |
| unpinAllChatMessages | chat_id* | True |
| leaveChat | chat_id* | True |

### Bot Management
| Method | Key Params | Returns |
|--------|-----------|---------|
| setMyCommands | commands*, scope, language_code | True |
| deleteMyCommands | scope, language_code | True |
| getMyCommands | scope, language_code | Array of BotCommand |
| setMyName | name, language_code | True |
| getMyName | language_code | BotName |
| setMyDescription | description, language_code | True |
| getMyDescription | language_code | BotDescription |
| setMyShortDescription | short_description, language_code | True |
| getMyShortDescription | language_code | BotShortDescription |
| setChatMenuButton | chat_id, menu_button | True |
| getChatMenuButton | chat_id | MenuButton |
| setMyDefaultAdministratorRights | rights, for_channels | True |
| getMyDefaultAdministratorRights | for_channels | ChatAdministratorRights |
| setManagedBotAccessSettings | business_connection_id*, settings* | True |
| getManagedBotAccessSettings | business_connection_id* | BotAccessSettings |
| getManagedBotToken | user_id* | String |
| getManagedBotAccessSettings | business_connection_id* | BotAccessSettings |

### Business & Guest
| Method | Key Params | Returns |
|--------|-----------|---------|
| answerGuestQuery | guest_query_id*, text/rich_message, parse_mode, entities, reply_markup | SentGuestMessage |
| getUserPersonalChatMessages | user_id* | Array of Message |
| getBusinessAccountGifts | ... | OwnedGifts |
| setBusinessAccountGiftSettings | ... | True |

### Other
| Method | Key Params | Returns |
|--------|-----------|---------|
| getUserProfilePhotos | user_id*, offset, limit | UserProfilePhotos |
| getFile | file_id* | File |
| answerCallbackQuery | callback_query_id*, text, show_alert, url, cache_time | True |
| getUpdates | offset, limit, timeout, allowed_updates | Array of Update |
| setWebhook | url*, certificate, ip_address, max_connections, allowed_updates, drop_pending_updates, secret_token | True |
| deleteWebhook | drop_pending_updates | True |
| getWebhookInfo | — | WebhookInfo |
| getChatMemberCount | chat_id* | Integer |

---

## 6. INLINE MODE TYPES

### InlineQuery
id, from, query, offset, chat_type ("sender"/"private"/"group"/"supergroup"/"channel"), location (opt)

### InlineQueryResult (20 types)
Article, Photo, Gif, Mpeg4Gif, Video, Audio, Voice, Document, Location, Venue, Contact, Game + 8 cached variants (CachedPhoto, CachedGif, CachedMpeg4Gif, CachedSticker, CachedDocument, CachedVideo, CachedVoice, CachedAudio)

### InputMessageContent (union)
- InputTextMessageContent: message_text, parse_mode, entities, link_preview_options
- InputRichMessageContent: rich_message
- InputLocationMessageContent: latitude, longitude, horizontal_accuracy, live_period, heading, proximity_alert_radius
- InputVenueMessageContent: latitude, longitude, title, address, foursquare_id, foursquare_type, google_place_id, google_place_type
- InputContactMessageContent: phone_number, first_name, last_name, vcard
- InputInvoiceMessageContent: title, description, payload, provider_token, currency, prices, max_tip_amount, suggested_tip_amounts, provider_data, photo_url, photo_size, photo_width, photo_height, need_name, need_phone_number, need_email, need_shipping_address, is_flexible, price_amount, price_label, subscription_period

### InlineQueryResultsButton
text, web_app (opt), start_parameter (opt)

### ChosenInlineResult
result_id, from, location (opt), inline_message_id (opt), query

---

## 7. PAYMENTS / TELEGRAM STARS

### Transaction Types
- TransactionPartnerUser: type="user", transaction_type, user, affiliate, invoice_payload, subscription_period, paid_media, paid_media_payload, gift, premium_subscription_duration
- TransactionPartnerChat: type="chat", chat, gift
- TransactionPartnerAffiliateProgram: type="affiliate_program", sponsor_user, commission_per_mille
- TransactionPartnerFragment: type="fragment", withdrawal_state
- TransactionPartnerTelegramAds: type="telegram_ads"
- TransactionPartnerTelegramApi: type="telegram_api", request_count
- TransactionPartnerOther: type="other"

### RevenueWithdrawalState (union: Pending/Succeeded/Failed)

### StarAmount
amount, nanostar_amount

### StarTransaction
id, amount, nanostar_amount, date, source, receiver

### StarTransactions
transactions (Array of StarTransaction)

### LabeledPrice
label, amount

### Invoice
title, description, start_parameter, currency, total_amount

### SuccessfulPayment
currency, total_amount, invoice_payload, subscription_expiration_date (opt), is_recurring (opt), is_first_recurring (opt), shipping_option_id (opt), order_info (opt), telegram_payment_charge_id, provider_payment_charge_id

### RefundedPayment
currency, total_amount, invoice_payload, telegram_payment_charge_id, provider_payment_charge_id

### ShippingQuery / PreCheckoutQuery / PaidMediaPurchased

---

## 8. TELEGRAM PASSPORT

### PassportData
data (Array of EncryptedPassportElement), credentials (EncryptedCredentials)

### PassportFile
file_id, file_unique_id, file_size, file_date

### EncryptedPassportElement
type ("personal_details"/"passport"/"driver_license"/"identity_card"/"internal_passport"/"address"/"utility_bill"/"bank_statement"/"rental_agreement"/"passport_registration"/"temporary_registration"/"phone_number"/"email"), data, phone_number, email, files, front_side, reverse_side, selfie, translation, hash

### PassportElementError (9 types)
DataField, FrontSide, ReverseSide, Selfie, File, Files, TranslationFile, TranslationFiles, Unspecified

---

## 9. GAMES

### Game
title, description, photo, text (opt), text_entities (opt), animation (opt)

### GameHighScore
position, user, score

### CallbackGame (placeholder, no fields)

---

## 10. STICKERS & GIFTS

### Sticker
file_id, file_unique_id, type ("regular"/"mask"/"custom_emoji"), width, height, is_animated, is_video, thumbnail (opt), emoji (opt), set_name (opt), premium_animation (opt), mask_position (opt), custom_emoji_id (opt), needs_repainting (opt), file_size (opt)

### StickerSet
name, title, sticker_type ("regular"/"mask"/"custom_emoji"), stickers (Array of Sticker), thumbnail (opt)

### MaskPosition
point ("forehead"/"eyes"/"mouth"/"chin"), x_shift, y_shift, scale

### InputSticker
sticker, format ("static"/"animated"/"video"), emoji_list, mask_position (opt), keywords (opt)

### Gift (regular)
id, sticker, star_count, upgrade_star_count (opt), is_premium (opt), has_colors (opt), total_count (opt), remaining_count (opt), personal_total_count (opt), personal_remaining_count (opt), background (GiftBackground, opt), unique_gift_variant_count (opt), publisher_chat (opt)

### UniqueGift
gift_id, base_name, name, number, model (UniqueGiftModel), symbol (UniqueGiftSymbol), backdrop (UniqueGiftBackdrop), is_premium (opt), is_burned (opt), is_from_blockchain (opt), colors (UniqueGiftColors, opt), publisher_chat (opt)

### GiftInfo / UniqueGiftInfo / OwnedGiftRegular / OwnedGiftUnique / OwnedGifts / AcceptedGiftTypes

---

## 11. FORMATTING STYLES

HTML tags supported: <b>, <i>, <u>, <s>, <spoiler>, <a href="">, <code>, <pre>, <tg-spoiler>, <blockquote>, <tg-sub>, <tg-sup>, <tg-emoji>, <tg-math>, <tg-blockquote>

Markdown supported: *bold*, _italic_, ~strikethrough~, ||spoiler||, `code`, ```pre```, [text](url), __underline__, ||spoiler||

---

## 12. COMMUNITIES (API 10.2)
- Community: id, name
- CommunityChatAdded: community
- CommunityChatRemoved: (no fields)
- Communities = linked supergroups + channels + bots

## 13. EPHEMERAL MESSAGES (API 10.2)
- Messages/commands visible only to specific user + bot
- Message.receiver_user, Message.ephemeral_message_id
- BotCommand.is_ephemeral
- Send params: receiver_user_id, callback_query_id
- ReplyParameters.ephemeral_message_id
- Dedicated edit/delete methods

## 14. GUEST MODE (API 10.0)
- Bot receives messages in chats it's not a member of
- User.supports_guest_queries
- Message.guest_bot_caller_user, guest_bot_caller_chat, guest_query_id
- Update.guest_message
- answerGuestQuery method
- SentGuestMessage

## 15. FILE DOWNLOAD
Base URL: `https://api.telegram.org/file/bot<token>/<file_path>`
Get file_path via getFile method.

---

*End of Telegram Bot API 10.2 Reference*
