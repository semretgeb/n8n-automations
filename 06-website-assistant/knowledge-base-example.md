# Semret Gebru: knowledge base for the website assistant

## Who I am
Semret Gebru is an n8n and AI automation developer based in Addis Ababa, Ethiopia (time zone UTC+3, East Africa Time). Semret has a BSc in Computer Science from HiLCoE School of Computer Science and Technology (2023 to 2026). Semret works in English and Amharic and is available more than 30 hours a week. Semret builds automations and AI agents that answer customers, capture leads and send daily reports, so small teams stop doing repetitive work by hand.

## Services
### AI customer support assistant
A Telegram (or WhatsApp, as a custom add-on) bot that answers customer questions 24/7 from the business's own data, such as a Google Sheet of products and an FAQ. It does not invent prices or stock. When it doesn't know the answer it hands the conversation to a human and alerts the owner.

### Lead capture and follow-up
Form or website leads are scored by AI (1 to 5) with a short reason and a suggested reply, saved to a CRM (Notion, Google Sheets, Airtable or HubSpot), and the owner gets an instant Telegram or Slack alert.

### Daily business reports
Every morning the workflow reads sales or operations data, calculates the key numbers in code (revenue, change from the day before, top products, pending payments) and an AI writes a short briefing with one suggested action. It is sent by Telegram and email.

### Document and receipt processing
PDF receipts and invoices from email are read automatically, the key fields (vendor, date, invoice number, currency, subtotal, tax, total, category) are extracted by AI into Google Sheets or Airtable, and a summary is posted to Slack. Fields that are not printed on the document are left empty rather than guessed.

### Content repurposing with approval
An article link is turned into a summary, a LinkedIn post and an X post. Drafts go to Notion for human approval before anything is posted.

### Custom automations
Connecting apps such as Gmail, Google Sheets, Google Calendar, Slack, Telegram, Notion, Airtable, HubSpot and webhooks or APIs, with or without an AI step.

## Pricing (starting points)
- Small automation (1 workflow, up to 2 apps): from $100, about 3 days.
- Standard automation (up to 4 apps plus an AI step): from $250, about 5 days.
- AI support assistant: from $150 for an FAQ bot, $350 with products, memory and human hand-off, $700 with lead capture and a daily summary.
- Hourly work: $25 per hour.
- Ongoing care plan (monitoring, fixes and one small change a month): from $150 per month.
Final prices depend on the scope; Semret confirms a fixed price after a short call.

## Running costs for clients
The AI part usually costs very little. Real measured examples: a daily sales report costs less than half a cent a month; processing 500 receipts costs about 7 cents a month; 2,000 support messages cost about 50 cents a month. n8n can be self-hosted for free, or run on a small server for about $5 to $7 a month.

## How Semret works
- A working first version in 2 to 4 days for most small projects.
- Every workflow has an error alert, so the client knows if anything stops.
- A short written update every day during the project.
- A hand-over video at the end, so the client's team can change things without Semret.
- Calculations and money-related logic stay in plain code; AI is used only for the wording, reading documents or classification, because AI can get numbers wrong.
- Nothing is sent to a customer without a check step when the message matters (draft, approve, then send).

## Tools
n8n (self-hosted and cloud), Make, Zapier, Python, Claude (Anthropic), OpenAI, Gemini and Groq models, Telegram Bot API, Google Workspace (Gmail, Sheets, Calendar, Drive), Notion, Airtable, HubSpot, Slack, Supabase, PostgreSQL, FastAPI.

## Projects
- ShopBot: a live Telegram shop platform for small businesses in Ethiopia, with a mini storefront inside Telegram, ordering, payment by transfer or on delivery, and an AI assistant that answers from each shop's data. Case study: https://semretgeb.github.io/shopbot/
- AI support agent on Telegram (n8n + Claude): demo https://youtu.be/0XWijngAmbA
- Receipt and invoice processing (Gmail to Google Sheets and Slack): demo https://www.loom.com/share/917972d9d74342f8a09829c4ca376c59
- AI content drafts with human approval (Notion to Slack): demo https://youtu.be/5yoprVV5yOE
- Automatic morning sales report on Telegram and email: demo https://youtu.be/ABhIhpixzJ8
- All workflows with setup notes: https://github.com/semretgeb/n8n-automations

## How to work with Semret
- Book a free 20-minute call through this chat to discuss a project.
- Or hire Semret on Upwork: https://www.upwork.com/freelancers/~0137b86b42a8cd4a9f

## Frequently asked questions
Q: Do I need to pay for n8n?
A: No. n8n can be self-hosted for free. Semret can host it on a small server (about $5 a month) or use your existing n8n or n8n Cloud.

Q: Can the bot work on WhatsApp?
A: Yes, as a custom add-on. WhatsApp needs a Meta business phone number, so this is discussed on a call first.

Q: Can you fix an existing workflow?
A: Yes. Fixing broken n8n, Make or Zapier workflows is a common small job. Share the error message and Semret can usually tell what is wrong quickly.

Q: What languages do you work in?
A: English and Amharic.

Q: Do you sign an NDA?
A: Yes, if the client needs one.

Q: How fast can you start?
A: Usually within 1 to 2 days.
