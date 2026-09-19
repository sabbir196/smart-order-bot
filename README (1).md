# TechNova — AI-Powered Telegram Sales & Support Chatbot (n8n)

An end-to-end e-commerce chatbot built in **n8n**, combining a **RAG knowledge base**, **multi-modal input handling** (text, voice, screenshots), and a **human-in-the-loop order approval flow** — all running inside Telegram.

This is a portfolio demo built around a fictional computer-hardware retailer, **TechNova**, showcasing a production-style automation pattern that can be adapted to any e-commerce brand.

---

## ✨ What it does

- Answers customer questions about products (price, specs, stock, warranty) using a **vector knowledge base**, never guessed/hallucinated data
- Understands **text, voice notes, and screenshots** — voice is transcribed and screenshots are described via Gemini before being handed to the agent
- Detects when a customer wants to order and **collects order details conversationally** (name, phone, email, address)
- Saves orders to **Google Sheets** and notifies a human representative by **email with Approve/Reject buttons** (human-in-the-loop — no order is confirmed without human sign-off)
- Sends the customer a **branded confirmation email** once approved, or a polite out-of-stock message if rejected
- Supports **order status checks and cancellations** mid-conversation
- Prevents duplicate knowledge-base entries when the product catalog is re-scraped
- Remembers conversation context per chat via **Postgres-backed chat memory**

---

## 🧱 Architecture

```
Telegram message
      │
      ▼
Detect Message Type (text / voice / photo)
      │
      ├─ voice  → Get Voice File → Gemini (transcribe) ─┐
      ├─ photo  → Get Screenshot → Gemini (vision)      ├─► Normalized text
      └─ text   → pass through ───────────────────────┘
                                        │
                                        ▼
                              AI Agent (Gemini + tool use)
                                        │
        ┌───────────────┬──────────────┼───────────────┬────────────────┐
        ▼               ▼              ▼                ▼                ▼
Knowledge Base    Save Order     notify_representative  Update Order   check_order_status
(Supabase RAG)   (Google Sheets)  (Gmail, Approve/Reject) Status (Sheets) (Sheets)
                                        │
                                        ▼
                              customer_confirm_message (Gmail)
                                        │
                                        ▼
                                Reply sent back via Telegram
```

A separate, schedulable pipeline (`Weekly Auto Scrape` / manual trigger) crawls product category pages, cleans the HTML, chunks it, embeds it, and stores it in Supabase — this is what powers the knowledge-base tool the agent calls.

---

## 🧰 Tech Stack

| Layer | Tool |
|---|---|
| Orchestration | [n8n](https://n8n.io) |
| LLM / Agent | Google Gemini (chat + vision + transcription) |
| Vector store | Supabase (pgvector) |
| Chat memory | Postgres |
| Messaging | Telegram Bot API |
| Order storage | Google Sheets |
| Notifications | Gmail (with built-in Approve/Reject buttons) |

---

## 📂 Repo Contents

| File | Purpose |
|---|---|
| `technova-telegram-chatbot-n8n.json` | The full n8n workflow (import into n8n) |
| `CREDENTIALS.md` | Checklist of every credential/API key you need to set up before importing |
| `sample-conversations.md` | Example customer ↔ bot exchanges showing the tone and flow |
| `screenshots/` | UI and conversation screenshots |

---

## ⚙️ Setup

1. Import `technova-telegram-chatbot-n8n.json` into your n8n instance.
2. Follow `CREDENTIALS.md` to connect each credential — the workflow will show red "credential missing" warnings on every node until this is done, that's expected.
3. In the **Set Category URLs** node, replace the placeholder URL with the real product category pages you want indexed.
4. Run the scraping branch once (manual trigger) to populate the Supabase `documents` table.
5. Activate the workflow and message your Telegram bot to test.

> ⚠️ This is a demo/portfolio project, not a production deployment. Review rate limits, error handling, and data privacy requirements before using it with real customer data.

---

## 🖼️ Demo

*(add your promo video link / GIF and screenshots here — see `screenshots/`)*

---

## 📄 License

MIT — feel free to fork and adapt for your own use case.
