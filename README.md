# n8n Automations

Practical n8n workflows for small businesses: lead capture with AI scoring, an AI support agent on Telegram, receipt processing from Gmail, content repurposing with approval in Notion, and a daily AI sales report. Each workflow is exported as JSON so it can be imported into your own n8n instance.

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

### 04. Content repurposing with approval: link → Claude → Notion → Slack (`04-content-repurposing/`)

Paste an article link into a form. Claude writes a summary, a LinkedIn post and an X post, and saves them to a Notion "Content Drafts" database for review. When a draft is set to Approved, a second workflow posts both drafts to Slack, ready to publish, and marks the row as Posted.

Two workflows:

- `content-repurposer.json`: n8n Form (URL, notes) → HTTP Request (fetch page) → Code (strip HTML, keep 12,000 characters) → Basic LLM Chain (Claude Haiku 5.5, structured output: title, summary, linkedin_post, x_post) → Notion page (Status: Needs review) → Slack alert
- `approved-drafts-to-slack.json`: Schedule (every minute) → Notion get pages → IF Status = Approved → Slack message with both drafts → Notion update (Status: Posted)

The approval step polls on a schedule rather than using the Notion trigger, because Notion's edit times are rounded to the minute and a quick approval can be missed.

Notion database columns: Title, Source URL, Summary, LinkedIn draft, X draft, Status (select: Needs review, Approved, Rejected, Posted).

Running cost: about $0.0007 per article with Claude Haiku 5.5.

### 05. Morning sales report: Google Sheets → Claude → Telegram and Gmail (`05-morning-report/`)

Every day at 07:00 the workflow reads a shop's orders, calculates yesterday's paid revenue, change from the day before, average order, pending orders and top products, and Claude turns the numbers into a short briefing with one suggested action. It is sent by Telegram and email.

Flow:

Schedule (07:00, workflow timezone set) → Google Sheets get rows → Code (daily stats) → Basic LLM Chain (Claude Haiku 5.5, uses only the given numbers) → Telegram message → Gmail message

`shop-orders-sample.csv` contains 155 sample orders for a demo shop (columns `order_id, date, customer, product, qty, unit_price_etb, total_etb, status`). Import it into a Google Sheet to try the workflow. The report covers the most recent day with orders before today, so the sample keeps working.

Running cost: about $0.00012 per day, under 1 cent a month.

## Setup

You need:
- A running n8n instance (Docker or n8n Cloud)
- An Anthropic API key
- A Notion integration with access to your leads database
- A Telegram bot token, and your chat ID for alerts
- A Tally form (workflow 01) and a published Google Sheet as CSV (workflow 02)
- Gmail and Google Sheets OAuth credentials, and a Slack app with the `chat:write` scope (workflows 03 to 05)

Steps:
1. Import the JSON file from the workflow folder into n8n (Workflows → Import from file). Import `shared/error-alert.json` (Telegram) or `shared/error-alert-slack.json` (Slack) too, and set it as the Error Workflow in each workflow's settings.
2. Create the credentials and connect them to each node.
3. Replace the placeholders: `OWNER_CHAT_ID`, `YOUR_NOTION_DATABASE_ID`, `YOUR_PRODUCTS_SHEET_CSV_URL`, `YOUR_FAQ_SHEET_CSV_URL` , `YOUR_RECEIPTS_SHEET_ID` and `YOUR_SHOP_ORDERS_SHEET_ID` (use the sheet's CSV export link: `https://docs.google.com/spreadsheets/d/<ID>/export?format=csv&gid=0`). For workflow 03, create a sheet with the header row `vendor, date, invoice_number, currency, subtotal, tax, total, category, source_email`, and pick your Slack channel in both Slack nodes. For workflow 04, create the Notion database described above and share it with your Notion integration. For workflow 05, set the workflow timezone in its settings and replace `you@example.com` with your email.
4. Activate the workflow.

## Notes

- Credentials are not included. Each export has its credential references removed, so you add your own keys after import.
- Replace `OWNER_CHAT_ID` and other IDs with your own values before use.

## Author

Semret Gebru. n8n and AI automation. Available for freelance work on Upwork.
