# Telegram Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `telegram`
**Base URL proxied:** `api.telegram.org`

## API Path Pattern

```
/telegram/:token/{method}
```

The `:token` placeholder is automatically replaced with the bot token from the connection configuration.

## Common Endpoints

### Get Bot Info
```bash
maton api '/telegram/:token/getMe'
```

### Get Updates
```bash
maton api -X POST '/telegram/:token/getUpdates' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "limit": 100,
  "timeout": 30
}
EOF
```

### Send Message
```bash
maton api -X POST '/telegram/:token/sendMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "text": "Hello!",
  "parse_mode": "HTML"
}
EOF
```

### Send Photo
```bash
maton api -X POST '/telegram/:token/sendPhoto' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "photo": "https://example.com/image.jpg",
  "caption": "Photo caption"
}
EOF
```

### Send Document
```bash
maton api -X POST '/telegram/:token/sendDocument' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "document": "https://example.com/file.pdf"
}
EOF
```

### Send Location
```bash
maton api -X POST '/telegram/:token/sendLocation' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "latitude": 37.7749,
  "longitude": -122.4194
}
EOF
```

### Send Poll
```bash
maton api -X POST '/telegram/:token/sendPoll' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "question": "What is your favorite?",
  "options": [{"text": "Option 1"}, {"text": "Option 2"}]
}
EOF
```

### Edit Message
```bash
maton api -X POST '/telegram/:token/editMessageText' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "message_id": 123,
  "text": "Updated text"
}
EOF
```

### Delete Message
```bash
maton api -X POST '/telegram/:token/deleteMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "message_id": 123
}
EOF
```

### Forward Message
```bash
maton api -X POST '/telegram/:token/forwardMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789,
  "from_chat_id": 123456789,
  "message_id": 123
}
EOF
```

### Get Chat
```bash
maton api -X POST '/telegram/:token/getChat' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "chat_id": 123456789
}
EOF
```

### Set Bot Commands
```bash
maton api -X POST '/telegram/:token/setMyCommands' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "commands": [
    {"command": "start", "description": "Start the bot"},
    {"command": "help", "description": "Get help"}
  ]
}
EOF
```

### Get File
```bash
maton api -X POST '/telegram/:token/getFile' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "file_id": "AgACAgQAAxkDAAM..."
}
EOF
```

### Set Webhook
```bash
maton api -X POST '/telegram/:token/setWebhook' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/webhook",
  "allowed_updates": ["message", "callback_query"]
}
EOF
```

### Answer Callback Query
```bash
maton api -X POST '/telegram/:token/answerCallbackQuery' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "callback_query_id": "12345678901234567",
  "text": "Button clicked!"
}
EOF
```

## Notes

- The `:token` placeholder is automatically replaced with the bot token
- Chat IDs are positive integers for private chats, negative for groups
- All methods support both GET and POST, but POST is recommended
- Text messages have a 4096 character limit
- Captions have a 1024 character limit
- Polls support 2-10 options
- Files can be sent via URL or file_id from previously uploaded files

## Resources

- [Telegram Bot API Documentation](https://core.telegram.org/bots/api)
- [Available Methods](https://core.telegram.org/bots/api#available-methods)
- [Formatting Options](https://core.telegram.org/bots/api#formatting-options)
