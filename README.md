# n8n Automations

Practical n8n workflows for small businesses: lead capture with AI scoring, an AI support agent on Telegram, and receipt processing from Gmail. Each workflow is exported as JSON so it can be imported into your own n8n instance.

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

Demo video: https://youtu.be/0XWijngAmbA

### 03. Receipt and invoice processing: Gmail → Google Sheets → Slack (`03-receipt-processing/`)

When an email with "receipt" or "invoice" arrives with a PDF attached, the workflow reads the PDF, extracts the key fields with an AI model, adds a row to a Google Sheet, and posts a summary to Slack.

Flow:

Gmail trigger (download attachments) → IF has attachment → Extract from File (PDF text) → Information Extractor (Claude Haiku 5.5, JSON schema) → Google Sheets append row → Slack message → Slack error alert on failure

Extracted fields: vendor, date, invoice number, currency, subtotal, tax, total, and category (food, transport, office, utilities, other). Any field not printed on the receipt is set to null, so the model never guesses. Each row also records the sender and subject of the source email.

Note: scanned or photographed receipts have no text layer, so they need an OCR step before extraction.

Demo video: https://www.loom.com/share/917972d9d74342f8a09829c4ca376c59

## Setup

You need:
- A running n8n instance (Docker or n8n Cloud)
- An Anthropic API key
- A Notion integration with access to your leads database
- A Telegram bot token, and your chat ID for alerts
- A Tally form (workflow 01) and a published Google Sheet as CSV (workflow 02)
- Gmail and Google Sheets OAuth credentials, and a Slack app with the `chat:write` scope (workflow 03)

Steps:
1. Import the JSON file from the workflow folder into n8n (Workflows → Import from file). Import `shared/error-alert.json` (Telegram) or `shared/error-alert-slack.json` (Slack) too, and set it as the Error Workflow in each workflow's settings.
2. Create the credentials and connect them to each node.
3. Replace the placeholders: `OWNER_CHAT_ID`, `YOUR_NOTION_DATABASE_ID`, `YOUR_PRODUCTS_SHEET_CSV_URL`, `YOUR_FAQ_SHEET_CSV_URL` and `YOUR_RECEIPTS_SHEET_ID` (use the sheet's CSV export link: `https://docs.google.com/spreadsheets/d/<ID>/export?format=csv&gid=0`). For workflow 03, create a sheet with the header row `vendor, date, invoice_number, currency, subtotal, tax, total, category, source_email`, and pick your Slack channel in both Slack nodes.
4. Activate the workflow.

## Notes

- Credentials are not included. Each export has its credential references removed, so you add your own keys after import.
- Replace `OWNER_CHAT_ID` and other IDs with your own values before use.

## Author

Semret Gebru. n8n and AI automation. Available for freelance work on Upwork.
