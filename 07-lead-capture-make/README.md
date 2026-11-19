# 07. Lead capture with AI scoring in Make.com

The same lead workflow as `01-lead-capture-ai-scoring`, built in Make.com instead of n8n. It shows the same idea working on a second platform.

## Flow

Tally form → Make custom webhook → Anthropic Claude (score 1–5, reason, suggested reply) → Parse JSON → Google Sheets row → Router:
- **Hot lead** (score ≥ 4): `🔥 Hot lead` Telegram alert
- **Fallback route**: `📩 New lead` Telegram alert

Error handling: the Claude and Parse JSON modules each have an error route that sends `⚠️ Lead scoring failed` on Telegram with the lead's name and the error message, then ends with **Ignore** so the scenario keeps running.

## How the form answers are read

Tally sends every question as an item in `data.fields[]`. The scenario finds each answer by its question label, so the order of the questions doesn't matter:

```
{{first(map(1.data.fields; "value"; "label"; "Name"))}}
```

The same formula is used with `Business`, `Phone` and `Message`.

## Getting clean JSON from Claude

The prompt ends with:

```
Respond with only one raw JSON object, nothing else. No markdown, no code blocks, no backticks. Start with { and end with }.
{"score": <1-5>, "reason": "<one short sentence>", "suggested_reply": "<a short friendly reply, max 40 words>"}
```

Parse JSON reads `{{2.content[].text}}` (the text of Claude's reply) with a data structure of `score` (number), `reason` (text) and `suggested_reply` (text). Thinking is disabled to keep the reply short and cheap.

## Google Sheet columns

`Date | Name | Business | Phone | Message | Score | Reason | Suggested reply`

Date uses `{{formatDate(now; "YYYY-MM-DD HH:mm")}}`. Set your time zone in your Make profile.

## Setup

1. In Make: **Create a new scenario → ⋯ → Import Blueprint** and choose `lead-capture-make.blueprint.json`.
2. Add your own connections: Anthropic Claude, Google Sheets and Telegram Bot. Credentials are not included in the file.
3. In the webhook module, create a new webhook and copy its address. In Tally, paste it under **Integrations → Webhooks**. The form needs questions titled exactly `Name`, `Business`, `Phone` and `Message`.
4. In Google Sheets, pick your spreadsheet (replace `YOUR_SPREADSHEET_ID`) with the header row above.
5. Replace `OWNER_CHAT_ID` in the four Telegram modules with your Telegram chat ID.
6. In Parse JSON, recreate the data structure if Make asks for it (`score`, `reason`, `suggested_reply`).
7. Turn **Scheduling** on with **Immediately**.

## Running cost

- Claude Haiku 5.5: about 250 input and 80 output tokens per lead, so about **$0.00007 per lead**.
- Make: about 5 operations per lead (webhook, Claude, Parse JSON, Sheets, Telegram). Check your Make plan's monthly allowance.
