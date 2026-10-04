# Elvion AI

Elvion AI is a Telegram AI assistant powered by the Gemini API and hosted on Cloudflare Workers.

Developer: Unique Engoke Lesley

## Features

- Natural conversation
- Recent per-chat conversation memory
- Clean Telegram inline menus
- `/start`, `/menu`, `/clear`, `/about`, `/status`, `/model`, `/help`
- Clean code formatting
- Removes unnecessary hashtag-heavy formatting
- Does not repeatedly introduce itself
- Telegram typing indicator
- Telegram message splitting for long answers
- Basic per-chat rate limiting
- Gemini retry handling for temporary API errors
- Cloudflare KV conversation storage
- Telegram webhook secret verification
- No API key hard-coded into the source

## 1. Create the Cloudflare Worker

Put the file here:

```text
elvion-ai/
├── src/
│   └── index.js
└── wrangler.toml
```

Deploy:

```bash
npx wrangler deploy
```

## 2. Create KV

```bash
npx wrangler kv namespace create BOT_KV
```

Cloudflare will return a namespace ID.

Put that ID into `wrangler.toml`:

```toml
[[kv_namespaces]]
binding = "BOT_KV"
id = "YOUR_REAL_KV_NAMESPACE_ID"
```

Then deploy again.

## 3. Add secrets

Do NOT put these values inside `index.js`.

```bash
npx wrangler secret put BOT_TOKEN
npx wrangler secret put GEMINI_API_KEY
npx wrangler secret put WEBHOOK_SECRET
```

Optional:

```bash
npx wrangler secret put OWNER_ID
```

`OWNER_ID` is reserved for future owner/admin features.

## 4. Set the webhook

After deployment, suppose your Worker is:

```text
https://elvion-ai.example.workers.dev
```

Open:

```text
https://elvion-ai.example.workers.dev/setup?key=YOUR_WEBHOOK_SECRET
```

The setup endpoint registers:

```text
https://elvion-ai.example.workers.dev/telegram
```

with Telegram.

## 5. Test

Open the Telegram bot and send:

```text
/start
```

Then ask a normal question.

Try:

```text
Explain JavaScript promises to me like a beginner.
```

Then follow up:

```text
Give me an example.
```

Elvion AI should understand that the second question refers to the previous topic.

## Gemini model

The default model is:

```text
gemini-2.5-flash
```

You can change it in `wrangler.toml`:

```toml
[vars]
GEMINI_MODEL = "your-model-name"
```

Use a Gemini model available to your API account.

## Important

Never commit these to GitHub:

- Gemini API key
- Telegram bot token
- Webhook secret

Use Cloudflare secrets instead.

## If Telegram returns 500

Check:

```bash
npx wrangler tail
```

Then send `/start` to the bot.

The Worker logs will show whether the problem is Telegram, Gemini, KV, or configuration.

## Commands

- `/start`
- `/menu`
- `/clear`
- `/about`
- `/status`
- `/model`
- `/help`
