# caption-pack-n8n-template

An importable [n8n](https://n8n.io) workflow that turns **one piece of content** into platform-ready captions + hashtags for X, LinkedIn, and Instagram — powered by the [Caption-Pack API](https://muse.ai/s/caption-pack-api-xfxt62ya0xcxlxhh). No LLM in the loop: deterministic template-and-variant engine, 1 credit per platform per run.

## What it does

Paste your source content (announcement, blog excerpt, key message) into a form → the workflow builds one API job per selected platform, each with a platform-appropriate tone (X → bold, LinkedIn → professional, Instagram → warm, or your own pick) → loops `POST /v1/caption-pack` once per platform → checks your credit balance → compiles everything into a single markdown **content plan** with a suggested posting order. Eight functional nodes, zero setup beyond an API key.

## Quickstart

1. **Get a free key** (25 calls, no card): open `https://caption-pack-api.onrender.com/v1/free-trial` in your browser. The key is shown **once** — save it somewhere safe.
2. **Import** `caption-pack-n8n-template.json` into n8n (Workflows → ⋯ → Import from File).
3. **Create a Header Auth credential**: Name `Authorization`, Value `Bearer ` followed by your key. Select it in the **Call Caption-Pack API** node **and** the **Check Balance** node.
4. **Test workflow**, fill the form, submit. Your multi-platform content plan lands on the completion page.

Check your balance anytime: `GET /v1/balance` with the same `Authorization` header.

## Nodes

| Node | Type | Purpose |
|---|---|---|
| ContentForm | Form Trigger | Source*, Audience*, Platforms dropdown, Tone dropdown, Count per platform |
| Build Platform Jobs | Code | One API job per platform; resolves per-platform tone defaults |
| Loop Over Platforms | Split In Batches | Runs the API call once per platform |
| Call Caption-Pack API | HTTP Request | `POST https://caption-pack-api.onrender.com/v1/caption-pack` |
| Collect Packs | Code | Gathers each platform's caption pack after the loop |
| Check Balance | HTTP Request | `GET https://caption-pack-api.onrender.com/v1/balance` |
| Build Content Plan | Code | Compiles per-platform captions into a markdown posting plan |
| Show Content Plan | Form Ending | Completion page with the full plan + credit summary |

(Plus nine sticky notes: an overview with setup instructions and one per step — laid out with no overlaps, verified in n8n 2.x.)

## Cost

- Each run costs exactly **1 credit per platform** (3 credits for all three).
- Free trial keys: 25 calls, no card, never top-up-able.
- Paid: **$10 for 500 calls** (2¢ each) via Stripe on the [landing page](https://muse.ai/s/caption-pack-api-xfxt62ya0xcxlxhh). Keys start with `cp_live_`, free keys with `cp_free_`.

**Like it?** Free 25 credits (no card) → $10 for 500. One Stripe click. No subscription: https://caption-pack-api.onrender.com/v1/checkout

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `401` from the API node | Key wrong, expired, or header misconfigured | Re-check the Header Auth credential: name must be `Authorization`, value `Bearer <key>` with one space |
| `402` + `insufficient_credits` | Out of credits | Top up via the `top_up_url` in the error response, or mint a fresh free key |
| `429` | Rate limited (free keys: 10 req/min) | Slow down the trigger or add a Wait node |

## Make it yours

- **Schedule trigger + Google Sheets**: daily caption ideas appended to a sheet — hands-off content pipeline.
- **Webhook trigger**: call it from your own app or agent with a JSON body `{topic, audience, tone, platform, count}`.

## API contract

Frozen until 2026-11-02. Full spec: `https://caption-pack-api.onrender.com/openapi.yaml`

## License

MIT — see [LICENSE](LICENSE) (or the text below):

> Permission is hereby granted, free of charge, to any person obtaining a copy
> of this software and associated documentation files (the "Software"), to deal
> in the Software without restriction, including without limitation the rights
> to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
> copies of the Software, and to permit persons to whom the Software is
> furnished to do so, subject to the following conditions:
>
> The above copyright notice and this permission notice shall be included in all
> copies or substantial portions of the Software.
>
> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
> IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
> FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
> AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
> LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
> OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
> SOFTWARE.
