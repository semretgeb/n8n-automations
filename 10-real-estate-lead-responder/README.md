# 10. AI real estate lead responder: form → Claude → instant email, CRM and Telegram

When a home buyer or seller fills in the agent's website form, the AI scores the lead, sends them a personal email reply within seconds, saves the lead in a Notion CRM, and alerts the agent on Telegram about the hot and warm ones. The agent who replies first usually wins the client; this replies first even during showings or at night.

The demo uses a fictional agent, Sarah Miller of Sunrise Realty in Austin, Texas.

## What it does

- **Qualifies** every lead from 1 to 5 based on timeline, financing (cash, pre-approved, talking to a lender), budget and their message: 4–5 is 🔥 Hot, 3 is 🌤 Warm, 1–2 is ❄️ Cold.
- **Replies instantly** by email in the lead's language, using what they wrote (area, budget, timeline), and ends with one clear next step: a 15-minute call, or a home value estimate for sellers.
- **Logs** the lead in a Notion CRM: score, temperature, AI summary, the agent's next step, the email that was sent, and how many seconds the reply took.
- **Alerts the agent** on Telegram for Hot and Warm leads only, with the phone number and the suggested next step.

```
🔥 HOT lead (5/5) · Buy
👤 Jane Smith · 📞 512-555-0147
📍 Round Rock · 💰 $300k - $500k
🗓 1-3 months · 🏦 Pre-approved

Pre-approved buyer looking for 3 bedrooms with a home office in Round Rock.
👉 Call today: ready to move within 3 months

✉️ Replied to the lead in 14 seconds.
```

## Flow

n8n Form (home inquiry) → Code (clean fields, note the arrival time) → Basic LLM Chain (Claude Haiku 5.5, thinking disabled, structured output: score, summary, next_step, email_subject, email_body) → Gmail (send the reply) → Code (temperature, reply time, alert text) → Notion (CRM row) → IF score ≥ 3 → Telegram alert

## Real estate rules built into the prompt

- **Fair Housing:** the reply never describes or compares areas by the people who live there (race, religion, national origin, family status, disability or "safety"). It talks only about homes, price, location features and the lead's own needs.
- No invented listings, prices, interest rates or market numbers, and no legal, tax or loan advice.
- The lead's text is treated as data, so instructions typed into the form are ignored.
- For texting leads instead of emailing them, US rules (TCPA) require the lead's consent, for example a checkbox on the form.

## Setup

1. Import `real-estate-lead-responder.json` into n8n.
2. Credentials: Gmail OAuth2 (the agent's mailbox), Anthropic, Notion and a Telegram bot.
3. Create a Notion database with these properties and share it with your Notion integration: Name (title), Temperature (select: Hot, Warm, Cold), Score (number), Looking to (select: Buy, Sell, Buy and sell), Area, Budget, Timeline, Financing (text), Email (email), Phone (phone), Message, AI summary, Next step, Reply sent (text), Replied in (sec) (number), Status (select: New, Contacted, Showing booked, Under contract, Closed, Lost), Received (date).
4. Replace `YOUR_NOTION_LEADS_DATA_SOURCE_ID` and `OWNER_CHAT_ID`.
5. Change the agent's details in **Qualify and reply** (name, brokerage, areas, next step offered), the form title and the email sender name.
6. Publish, then share the form's production URL or embed it on the agent's website.

## Cost

About $0.0004 per lead with Claude Haiku 5.5.
