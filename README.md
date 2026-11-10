# n8n Automations

Practical n8n workflows for small businesses: lead capture with AI scoring, and an AI support agent on Telegram. Each workflow is exported as JSON so it can be imported into your own n8n instance.

## Workflows

### 01. Lead capture with AI scoring: Tally → Notion → Telegram (`01-lead-capture-ai-scoring/`)

When someone fills in a Tally form, the workflow scores the lead with an AI model, saves it to a Notion CRM, and sends an alert to Telegram with a suggested reply.

Flow:

Tally webhook → Edit Fields (extract Name, Business, Phone, Message) → Basic LLM Chain (score 1–5, reason, suggested reply) → Notion database row (Status: New) → Telegram alert → Error alert on failure

Lead scoring:
- Score 4 or 5 is marked "🔥 Hot lead".
- Lower scores are marked "📩 New lead".

Each Notion row stores the name, business, phone, message, score, AI note and status.

### 02. AI support agent on Telegram (`02-ai-support-agent-telegram/`)

A Telegram bot that answers customer questions from a product list and FAQ kept in Google Sheets. When it doesn't know the answer, it hands the conversation to the shop owner.

Flow:

Telegram trigger → AI Agent (Claude Haiku 5.5, with conversation memory and two Sheet tools: Products and FAQ) → if the reply contains `[HANDOFF]`, alert the owner; otherwise reply to the customer

The agent is instructed to answer only from the sheet data, so it doesn't invent prices or stock.

## Setup

You need:
- A running n8n instance (Docker or n8n Cloud)
- An Anthropic API key
- A Notion integration with access to your leads database
- A Telegram bot token, and your chat ID for alerts
- A Tally form (workflow 01) and a published Google Sheet as CSV (workflow 02)

Steps:
1. Import the JSON file from the workflow folder into n8n (Workflows → Import from file). Import `shared/error-alert.json` too, and set it as the Error Workflow in each workflow's settings.
2. Create the credentials and connect them to each node.
3. Replace the placeholders: `OWNER_CHAT_ID`, `YOUR_NOTION_DATABASE_ID`, `YOUR_PRODUCTS_SHEET_CSV_URL` and `YOUR_FAQ_SHEET_CSV_URL` (use the sheet's CSV export link: `https://docs.google.com/spreadsheets/d/<ID>/export?format=csv&gid=0`).
4. Activate the workflow.

## Notes

- Credentials are not included. Each export has its credential references removed, so you add your own keys after import.
- Replace `OWNER_CHAT_ID` and other IDs with your own values before use.

## Author

Semret Gebru. n8n and AI automation. Available for freelance work on Upwork.
