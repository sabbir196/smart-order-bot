# Credential Setup Checklist

This workflow was sanitized before publishing — all credential IDs, the real notification email, and the real Google Sheet ID were removed. To run it yourself, connect the following in n8n:

| # | Credential | Used by | How to get it |
|---|---|---|---|
| 1 | **Telegram Bot API** | Telegram Trigger, Get Voice File, Get Screenshot File, Send a text message | Create a bot via [@BotFather](https://t.me/BotFather), copy the token |
| 2 | **Google Gemini (PaLM) API** | Embeddings, Chat Model, Gemini transcription/vision HTTP calls | Get an API key from [Google AI Studio](https://aistudio.google.com/) |
| 3 | **Supabase** | Supabase Vector Store (insert + retrieve) | Create a project at [supabase.com](https://supabase.com), enable the `pgvector` extension, get your project URL + service key |
| 4 | **Postgres** | Postgres Chat Memory | Any Postgres instance (Supabase's own Postgres connection works too) |
| 5 | **Google Sheets OAuth2** | save order, Update Order Status, check_order_status | Connect via OAuth2 in n8n; create a sheet with columns: `Order id, name, email, phone number, product details, Address, status, order time` |
| 6 | **Gmail OAuth2** | notify_representative, customer_confirm_message | Connect via OAuth2 in n8n; used to send the approval-request email and the customer confirmation email |

## Also update manually

- [ ] `notify_representative` node → replace the placeholder recipient with your own email
- [ ] Google Sheets node → point `documentId` at your own sheet
- [ ] `Set Category URLs` node → replace the placeholder URL with real product category pages
- [ ] Verify the Gemini model names (`gemini-3.5-flash` / `gemini-3.6-flash`) against your current available models — these may need updating depending on API version at the time you deploy

## Supabase table schema

```sql
create table documents (
  id bigserial primary key,
  content text,
  metadata jsonb,
  embedding vector(768) -- match this to your embedding model's output dimension
);
```
