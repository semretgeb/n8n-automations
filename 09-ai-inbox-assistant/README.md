# 09. AI email inbox assistant: Gmail → Claude → labels, reply drafts and Telegram

Every new email is sorted by AI, labelled in Gmail, and answered with a reply draft when a real person is waiting for an answer. Leads and urgent emails trigger a Telegram alert, and an evening summary shows what came in. Nothing is sent automatically: replies stay as drafts until you review them and press Send.

## What it does

- **Sorts** each new email into lead, client, support, invoice, newsletter, spam, personal or other, with a priority (high, normal, low) and a one-sentence summary.
- **Labels** it in Gmail as `AI/Lead`, `AI/Invoice` and so on.
- **Drafts a reply** in the same Gmail thread for leads, clients, support and personal emails, in the sender's language. The AI uses only the facts you give it about your business, so it never invents prices, dates or promises.
- **Alerts you on Telegram** for leads, clients and anything high priority, with the summary and the draft.
- **Sends an inbox summary** every day at 18:00: counts per category, drafts waiting, and who needs you.

```
🔥 LEAD · high priority
👤 Hana
✉️ Need a Telegram bot for my shop

Wants a price for a Telegram shop bot.

✍️ Reply draft is waiting in Gmail:
Hi Hana, thanks for reaching out! ...
```

## Flow

- **Gmail trigger** (every minute, unread inbox emails, promotions and social tabs skipped) → **Clean email** (Code: sender, subject and the new text only; quoted history cut, 4,000 characters max) → **Sort and draft** (Basic LLM Chain, Claude Haiku 5.5 with thinking disabled, structured output: category, priority, summary, needs_reply, reply) → **Get labels** → **Attach label** → **Add label** → **Needs reply?** → **Create reply draft** (in the same thread) → **Remember** (log for the summary) → **Alert me?** → **Telegram alert**
- **Every day 18:00** → **Build summary** → **Send summary** (Telegram)
- **Create labels (run once)** → creates the 8 `AI/...` labels in Gmail

## Safety

- Emails are treated as data: the prompt tells the model to ignore any instructions inside an email.
- No-reply senders, newsletters, spam and invoice notifications never get a draft, even if the model suggests one.
- Phishing and "verify your account" emails are labelled spam, with the reason in the summary.
- Replies are drafts only. The workflow has no node that sends email.

## Setup

1. Import `ai-inbox-assistant.json` into n8n.
2. Credentials: Gmail OAuth2, Anthropic and a Telegram bot. Connect them to the Gmail, Claude and Telegram nodes.
3. In **Sort and draft**, replace the ABOUT THE OWNER text with real facts about you or the business (services, how prices are agreed). Replies use only these facts.
4. Replace `OWNER_CHAT_ID` in the two Telegram nodes.
5. Run **Create labels (run once)** once.
6. Set the workflow timezone and the Error Workflow in its settings, then publish. The summary log is kept in workflow static data, which n8n saves in production runs only.

Google tip: if your Google Cloud OAuth app is in **Testing** mode, Google cancels the Gmail login after 7 days (`invalid_grant`). Publish the app (OAuth consent screen → Publish app) and reconnect once.

## Cost

About $0.0003 per email with Claude Haiku 5.5, so 1,000 emails cost about $0.30.
