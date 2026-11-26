# 11. AI lead finder + outreach: map search → website → Claude → Google Sheet and Telegram

Type a business type, a city and what you sell into a form. The workflow finds matching businesses on the map, reads each one's website, and Claude writes a personal first line for a cold email using only facts from that site. Everything lands in a Google Sheet, ready to send, and a summary arrives on Telegram. Instead of an hour of copying names and websites by hand, you get 20 researched leads in a few minutes.

## What it does

- **Finds businesses** of the chosen type in the city (dentists, clinics, restaurants, cafes, hotels, gyms, beauty salons, real estate agencies, lawyers) using OpenStreetMap, which is free and needs no API key.
- **Keeps only businesses with a website**, removes duplicates, and collects the phone, email and address when they are listed.
- **Reads each website** and also picks up an email address from the page if the map didn't have one.
- **Writes a first line** with Claude Haiku 5.5: one sentence, written to the business ("you", "your"), that mentions something real from their site and connects it to your offer. It also gives a short "what they do" and a fit rating (high, medium, low).
- **Saves to Google Sheets**, updating rows by website, so running the same search twice doesn't create duplicates.
- **Sends a Telegram summary** with the number of leads, how many are a high fit, and the best first lines.

```
🔎 Lead finder done: Real estate agency in Manchester

✅ 5 leads saved
🔥 3 high fit
⚠️ 2 websites could not be read

• Martin & Co: With someone choosing to sell or let with you every 8 minutes, a 60-second reply to every property enquiry could keep more leads warm.
• JP & Brimelow: For your Chorlton, Didsbury and Withington enquiries, an assistant replying within 60 seconds, 24/7, could stop serious buyers going cold.
```

Both facts come from the agencies' own websites ("Someone chooses to sell or let a property with us every 8 minutes", "We cover Chorlton, Didsbury and Withington").

## Flow

n8n Form (business type, city, your offer, max leads) → Code (build the map query) → HTTP Request (OpenStreetMap Overpass API, retries, then a backup server on error) → Code (keep businesses with a website, remove duplicates) → HTTP Request (read each website, 5 at a time, continue on error) → Code (strip HTML, find emails, keep 3,000 characters) → Basic LLM Chain (Claude Haiku 5.5, thinking disabled, structured output: first_line, what_they_do, fit) → Code (sheet row and status) → Google Sheets (append or update by Website) → Code (summary) → Telegram

## Built to be reliable

- **Fact check on every line.** Claude must also return the exact words from the website that its first line is based on. A Code step checks that those words really appear on the page; if not, or if the line is over 30 words, the lead is saved as "Check manually" instead of "Ready to send". This catches invented quotes; it cannot prove the sentence says exactly what the quote says, so read each line before you send it.
- **No invented facts.** The prompt allows only facts from the website text. In testing, one listed website belonged to a different hotel in another country; Claude said so and left the first line empty instead of making one up.
- **Dead or blocked websites don't stop the run.** They are saved with the status "Check manually", so you still keep the phone number and address.
- **Busy map server.** The search retries 5 times, then switches to a backup Overpass server automatically.
- **Honest status.** A row is "Ready to send" only when the website was read and a first line was written.
- **Clear error.** If nothing is found, the run stops with "No [type] with a website found in [city]" and the error workflow sends a Telegram alert.

## Setup

1. Import `ai-lead-finder.json` into n8n.
2. Credentials: Anthropic, Google Sheets OAuth2 and a Telegram bot.
3. Create a Google Sheet with this header row: `Date, Business, Type, City, Website, Phone, Email, Address, What they do, First line, Fit, Status`.
4. Replace `YOUR_GOOGLE_SHEET_ID` in **Save to Sheet** (or pick your sheet from the list) and `OWNER_CHAT_ID` in **Tell me on Telegram**.
5. Optional: add more business types in **Build search** (any OpenStreetMap tag, for example `shop=car_repair`) and in the form's dropdown.
6. Publish, then open the form's production URL.

## Good to know

- Use the city's English name as it appears on the map: `Addis Ababa`, `Manchester`, `Greater London`. Everyday names sometimes differ from the official area name.
- OpenStreetMap coverage depends on the city. UK and European cities have many businesses with websites; in Addis Ababa, hotels and restaurants have the most. For wider coverage, the search step can be switched to the Google Places API.
- The public Overpass servers are shared and free. For heavy daily use, run your own Overpass instance or use Google Places.
- Respect the rules for cold outreach where you send (for example GDPR and PECR in the UK): contact businesses on their business addresses, say who you are, and include an easy way to opt out.

## Cost

About $0.0002 per lead with Claude Haiku 5.5. The map search is free.
