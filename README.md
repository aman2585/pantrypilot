<h1 align="center">PantryPilot</h1>
<p align="center"><em>Speak your groceries. In any language. The cart fills itself.</em></p>

<p align="center">
  <a href="#-demo">Demo</a> ·
  <a href="#-why-i-built-this">Why</a> ·
  <a href="#-how-it-works">How it works</a> ·
  <a href="#-key-decisions">Decisions</a> ·
  <a href="#-limitations--roadmap">Roadmap</a>
</p>

---

## 🎬 Demo

> ▶️ **[Watch the demo video](https://drive.google.com/file/d/1QsFht6_QpY5vZAVHCBjR-gjXJe4wl_wX/view?usp=sharing)**

Send a voice note in Hindi, Tamil, English, or a mix — and PantryPilot understands it, finds the products on Swiggy Instamart, adds them to your real cart, and sends back a clear, itemised confirmation. You review and check out yourself.

---

## 💡 Why I built this

Think about your parents or grandparents.

They spend hours a day on WhatsApp — texting, sending voice notes, forwarding good-morning messages. They're completely at home there.

But ask them to order groceries on an app like Instamart or Blinkit, and it's a different story. Small fonts, endless menus, search bars, filters, product variants. For a lot of older people, that's a wall — and the research backs this up: surveys of Indian seniors find a majority feel digital tools are confusing, and app *navigation* (not the idea of shopping online) is a big part of why. They happily use messaging apps but tend to avoid shopping and payment apps.

So I asked a simple product question:

> **What if ordering groceries could be as easy as sending a voice note — in your own language — on an app you already use every day?**

That's PantryPilot. It meets people where they already are (a chat app), in the way that's most natural to them (speaking), and does the hard part (navigating the store) for them.

It's aimed first at **seniors and less-tech-savvy users who are comfortable in chat but bounce off shopping apps** — though honestly, anyone who'd rather speak a list than tap through a store can use it.

---

## 🧠 How it works

PantryPilot is an AI agent built as an automation workflow. Here's the pipeline, in plain terms:

```
Voice / text (Telegram)
        │
        ▼
  Transcribe to text        →  handles English, Hindi, Hinglish, Tamil…
        │
        ▼
  Understand & extract       →  an LLM pulls out the grocery items and
  the grocery items             translates them to standard English names
        │                        ("doodh" → "milk")
        ▼
  Search Swiggy's real       →  via Swiggy's official MCP server
  catalogue (per item)
        │
        ▼
  Pick the best product      →  an LLM chooses a sensible everyday option —
  for each item                 skips sponsored ads, avoids odd bulk packs
        │
        ▼
  Add to the real cart       →  via Swiggy MCP
        │
        ▼
  Itemised confirmation      →  "✅ Added: milk, bread…  ⚠️ Couldn't find: X"
  back in the chat
```

**The stack, briefly:**

| Piece | Role |
|---|---|
| **Telegram Bot API** | The interface — where the user speaks or types |
| **Speech-to-text (Whisper)** | Turns the voice note into text, in any language |
| **LLMs** | Two jobs: understanding the request, and picking the best product |
| **Swiggy MCP** | An official "doorway" that lets the agent use Swiggy's *real* search and cart — not a mock |
| **n8n** | The workflow engine that wires it all together |

> **What's MCP?** Model Context Protocol — an open standard that lets an AI agent use an app's real tools. Here, it's how the agent actually searches Swiggy and touches a live cart.

---

## 🎯 Key decisions

These are the choices I'm most proud of — they're where the product thinking lives.

**1. Fill the cart, but never check out.**
The agent adds items to the cart — but it *never* pays or places the order. The person always reviews and checks out themselves. This is deliberate: research consistently shows that **payments are exactly where older users hesitate and lose trust.** Keeping a human in the loop for the money makes the agent feel safe, not scary. The AI does the tedious part; the person stays in control.

**2. Shop like a thoughtful person, not a robot.**
When there are several products for "milk," the agent doesn't grab the first result. It skips sponsored/ad products, avoids weird bulk packs, and picks a sensible everyday option — the way a careful human shopper would.

**3. Speak any language; store it in one.**
People speak naturally — "do packet doodh aur cheeni." The agent understands the mixed language and normalises item names to English internally, so catalogue search stays reliable. The user never has to change how they talk.

**4. Be honest when it can't help.**
If something's unavailable, it says so — "⚠️ Couldn't find: squid" — instead of silently dropping it or adding the wrong thing. And if the message has no groceries in it at all, it gently asks for a proper list rather than failing silently.

**5. Telegram to prototype; WhatsApp to scale.**
I built on Telegram because its bot platform is free and instant — no fees, no approval process — which is perfect for proving an idea. The exact same concept extends to **WhatsApp**, which is where the real target users already are (at the cost of approval + per-message fees). That's the natural next step for reaching them at scale — a classic build-fast-then-scale trade-off.

---

## ⚠️ Limitations & roadmap

Being honest about what's *not* done is part of the work. Current known limitations:

- **Runs on demand, not fully hands-off.** The agent runs when triggered. A truly always-on version would use webhooks (or a message queue) rather than polling — that's the main production-hardening step.
- **Tangential substitutes can slip through.** Product-matching against a live catalogue is genuinely hard — searching for an unavailable item sometimes returns a loosely-related product (e.g. "squid" → a squid-flavoured snack). A stricter relevance check is the fix.
- **No store-closed / token-expiry messaging yet.** If the store's shut or an access token expires, it should tell the user gracefully — planned.
- **WhatsApp not yet wired.** Telegram-first for prototyping; WhatsApp is the scale target.

None of these are hard blockers — they're the difference between "a working proof of concept" and "a hardened product," and they're exactly what a v1.1 would tackle.

---

## 🛠 Running it yourself

> This is a personal project built on a self-hosted [n8n](https://n8n.io) instance. To adapt it you'll need your own credentials (a Telegram bot token, an LLM API key, and Swiggy MCP access). The workflow JSON in this repo has all secrets removed — replace the placeholders with your own.

1. Install and run n8n.
2. Create a Telegram bot via **@BotFather** and get its token.
3. Set up your LLM API key (for transcription + understanding).
4. Connect Swiggy's MCP endpoint (see Swiggy's MCP docs).
5. Import `workflow/pantrypilot.json` and fill in your credentials where marked.

*(See `.env.example` for the full list of values you'll need.)*

---

## 📌 In one line

> I identified an under-served segment — seniors locked out of grocery apps by app-first design — and matched a voice-and-chat interface to their real constraints. This working agent is the proof that the right interface can quietly bring a whole group into modern convenience.

---

<p align="center"><sub>Built as a portfolio project exploring AI product thinking, agentic tool-use (MCP), and interface-to-user fit.</sub></p>
