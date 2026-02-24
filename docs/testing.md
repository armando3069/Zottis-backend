Test results — all green

Endpoint: POST /telegram/connect                                                                                                                                            
Result: ✅ Calls Telegram getMe, upserts platform_accounts, strips access_token from response, generates fresh webhookSecret on re-connect                                     
────────────────────────────────────────                                                                                                                                      
Endpoint: GET /telegram/conversations
Result: ✅ Returns empty [] before any messages; returns owned conversations after webhook; returns 401 without token
────────────────────────────────────────
Endpoint: POST /telegram/webhook/:botId
Result: ✅ Creates conversation + message on first hit; re-uses same conversation on repeat hits (correct idempotency)
────────────────────────────────────────
Endpoint: GET /telegram/conversations/1/messages
Result: ✅ Returns 2 inbound messages in timestamp order
────────────────────────────────────────
Endpoint: POST /telegram/reply
Result: ✅ Auth + ownership check pass; Telegram API call fires with real bot token; "chat not found" is Telegram's own error for the fake test chat_id — expected
────────────────────────────────────────
Endpoint: Auth isolation
Result: ✅ Every user-facing endpoint returns 401 without a valid JWT

Two .env things to remember for production

# Swap these when you expose the server publicly (e.g. via ngrok / Railway / fly.io)
APP_URL=https://your-public-domain.com
# Remove or set to false — webhook registration will then fire on connect
SKIP_WEBHOOK_REGISTRATION=false

When SKIP_WEBHOOK_REGISTRATION=false (production), POST /telegram/connect will also call Telegram's setWebhook so that inbound messages flow into POST
/telegram/webhook/:botId automatically.