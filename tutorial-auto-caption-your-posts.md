# Auto-caption your posts with n8n + Caption-Pack (5 minutes, free)

**DRAFT — needs Jeshua's approval before publishing (dev.to: jeshua_domingo_8869c1283c)**
Suggested tags: `n8n`, `api`, `automation`, `tutorial`

---

Last week I launched a small API that turns a topic, audience, and tone into ready-to-post social captions — no LLM in the loop, just a deterministic template-and-variant engine. It works. But an API only helps people who wire it up, so today I'm sharing the easiest on-ramp I know: an 8-node n8n workflow that turns **one piece of content** into platform-ready posts for X, LinkedIn, and Instagram in about five minutes.

If you've never touched n8n, this is a friendly first workflow. If you live in n8n, steal it and make it yours.

## What you'll build

A form where you paste your source content (an announcement, a blog excerpt, a key message), pick your platforms and tone — and get back a complete **multi-platform content plan**: captions + hashtags for each platform, each in a platform-appropriate tone (X → bold, LinkedIn → professional, Instagram → warm — or your own pick), plus a suggested posting order and your remaining credit balance. Each platform costs 1 API credit per run.

## Step 1: Get a free API key

Open this in your browser:

```
https://caption-pack-api.onrender.com/v1/free-trial
```

It hands you a key with **25 free calls, no card**. The key is shown once, so save it somewhere safe (a password manager works fine).

![Free-trial API response with the key redacted](screenshots/shot1-free-trial.png)

## Step 2: Import the workflow

Grab the template JSON from the repo:

**https://github.com/jeshuadomingo-byte/caption-pack-n8n-template**

In n8n: **Workflows → ⋯ → Import from File**, and select `caption-pack-n8n-template.json`. You'll see eight nodes plus friendly sticky notes:

- **ContentForm** — the input form (source content, audience, platforms, tone, count per platform)
- **Build Platform Jobs** — a Code node that builds one API job per platform with per-platform tone defaults
- **Loop Over Platforms** — runs the API call once per platform
- **Call Caption-Pack API** — the HTTP request
- **Collect Packs** — gathers each platform's caption pack
- **Check Balance** — reads your remaining credits
- **Build Content Plan** — compiles everything into one markdown posting plan
- **Show Content Plan** — the completion page

![Imported workflow canvas](screenshots/shot2-workflow-canvas.png)

## Step 3: Connect your key

n8n strips credentials on export (by design — never share keys in a template), so you wire yours in once:

1. In n8n, go to **Credentials → New → Header Auth**.
2. Set **Name** to `Authorization` and **Value** to `Bearer ` followed by your key (one space after `Bearer`).
3. Open the **Call Caption-Pack API** node **and** the **Check Balance** node, and select that credential in both.

![Header Auth credential configuration](screenshots/shot3-header-auth.png)

## Step 4: Run it

Click **Test workflow**, fill in the form (try pasting a short announcement as the source, audience "remote workers", platforms "All three", tone "Auto"), and submit. Your multi-platform content plan lands on the completion page — per-platform captions with hashtags, a suggested posting order, and your credit balance underneath.

![Completed form with content plan](screenshots/shot4-form-complete.png)

That's it. Five minutes, zero dollars.

## Make it yours

The Form trigger is the friendliest demo, but the real fun starts when you swap it:

- **Schedule trigger + Google Sheets** — generate a fresh batch of caption ideas every morning and append them to a sheet. Hands-off content pipeline.
- **Webhook trigger** — `POST` a JSON body like `{"topic": "...", "audience": "...", "tone": "bold", "platform": "x", "count": 5}` from your own app or AI agent, and get captions back. This is the lane I'm most excited about: agents calling a deterministic caption engine instead of burning LLM tokens.

## The cost math

1 credit per platform per run (3 for all three). $10 gets you 500 calls — 2¢ per platform, about 6¢ for the full three-platform run. The free tier covers this entire tutorial with room to spare.

## Troubleshooting

| What you see | What it means | Fix |
|---|---|---|
| `401` | Key wrong, expired, or header misconfigured | Check the Header Auth credential — name `Authorization`, value `Bearer <key>` |
| `402 insufficient_credits` | Out of credits | The error includes a `top_up_url`, or mint a fresh free key |
| `429` | Rate limited (free keys: 10 req/min) | Slow down, or add a Wait node between runs |

## Why no LLM?

Deterministic means predictable: same inputs, same quality, every time — and at about 6¢ for the full three-platform run, it's cheaper than any model call that could do the job. There's a time for LLMs; caption drafting at volume isn't it.

---

The template is MIT-licensed. If you build something with it, I'd genuinely love to hear about it — that's what keeps a project like this alive.

Full API contract: `https://caption-pack-api.onrender.com/openapi.yaml`
Try the API with no key at all: the keyless playground is on the [landing page](https://muse.ai/s/caption-pack-api-xfxt62ya0xcxlxhh).
