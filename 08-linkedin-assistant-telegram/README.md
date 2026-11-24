# 08. AI LinkedIn assistant you control from Telegram

Every weekday at 4 pm (or any time with `/post`), a Telegram bot asks what I built that day. I reply in the chat, Claude writes a LinkedIn post from my notes and a Notion page of real facts about my work, and the bot shows me the draft. I can ask for changes in plain words, paste my own wording, or approve it. Approved posts go to LinkedIn and are logged in Notion.

## The conversation

```
Bot: 📝 LinkedIn post: Build log
     What did you build today? What broke, and how did you fix it?
Me:  built the same lead workflow in n8n, Make and Zapier
Bot: ✍️ LinkedIn draft …            [✅ Approve] [⏭ Skip]
Me:  make it shorter and start with the error
Bot: ✍️ LinkedIn draft (new version) …
Me:  ✅ Approve
Bot: ✅ Posted on LinkedIn and saved in Notion.
```

Commands: `/post` starts a post, `edit: <text>` replaces the draft with my own wording, `⏭ Skip` or `/cancel` stops, `/help` lists everything.

## Flow

Two triggers share one workflow:

- **Weekdays 4 pm** (Schedule) → pick today's question (Monday plan, Tuesday/Thursday build log, Wednesday tip, Friday result) → ask on Telegram
- **Telegram message** → only my chat ID is accepted → read my reply → route:
  - `ask`: send the question
  - `draft`: read the Notion facts page → Claude Haiku 5.5 writes or revises the post → save the draft → show it with ✅ Approve / ⏭ Skip buttons
  - `show`: show my own edited text for approval
  - `post`: LinkedIn post → Notion log (LinkedIn Posts database) → "✅ Posted"
  - `say`: short replies (help, skip, nothing in progress)

The conversation state (waiting for notes, or waiting for approval, plus the current draft) is kept in the workflow's static data, so no extra database is needed. Static data is only saved in production runs, so the workflow must be published to work.

## Why a facts page

A short note like "write about last week" gives the model nothing to work with, and a model that isn't allowed to invent things will refuse. The Notion facts page lists real projects, problems solved and measured costs, one per line. The prompt allows only those facts and my notes, so posts stay specific and true. I add new facts after each project.

See `linkedin-facts-example.md` for the format.

## Setup

1. Import `linkedin-assistant-telegram.json` into n8n.
2. Credentials: a Telegram bot (its own bot, because only one workflow can receive a bot's messages), Anthropic, Notion and LinkedIn OAuth2.
3. LinkedIn app: create one at linkedin.com/developers (it must be linked to a Company Page), add the products **Sign In with LinkedIn using OpenID Connect** and **Share on LinkedIn**, and add the n8n OAuth callback URL. In the n8n LinkedIn credential turn **Legacy** and **Organization Support** off, otherwise LinkedIn rejects the scopes.
4. Replace the placeholders: `OWNER_CHAT_ID` (in the Only me? check and the Telegram nodes), `YOUR_LINKEDIN_PERSON_ID` (pick yourself in the LinkedIn node), `YOUR_NOTION_FACTS_PAGE_ID` and `YOUR_NOTION_POSTS_DATA_SOURCE_ID`.
5. Notion: a page for the facts and a database with the properties Title, Post, Type (select: Plan, Build log, Tip, Result), Notes, Status (select: Posted, Skipped, Failed) and Date.
6. Set the workflow timezone, then publish once. If publishing fails, don't keep retrying: repeated attempts make Telegram rate-limit the bot.

## Notes

- LinkedIn tokens from standard apps last 60 days; reconnect the credential when the error alert arrives.
- Running cost: about $0.0005 per post with Claude Haiku 5.5 (thinking disabled).
