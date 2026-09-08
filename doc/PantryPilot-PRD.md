# Product Requirements Document — PantryPilot

| | |
|---|---|
| **Product** | PantryPilot — voice-first grocery ordering agent |
| **Author** | Aman |
| **Status** | v1.0 — working prototype |
| **Last updated** | September 2026 |
| **Type** | Personal / portfolio project |

---

## 1. Overview

PantryPilot lets a person order groceries by sending a voice note or text message — in their own language — through a chat app they already use. The agent understands the request, finds the products on Swiggy Instamart, adds them to the user's cart, and returns a confirmation. The user reviews the cart and checks out themselves.

The goal is to remove the biggest barrier that keeps less-tech-savvy people off grocery apps: navigation. Instead of learning an app, the user just says what they want.

---

## 2. Problem statement

Quick-commerce grocery apps (Instamart, Blinkit, Zepto) are designed for app-fluent users. For a large and growing segment — older adults and less-tech-savvy users — the interface itself is the barrier: small fonts, layered menus, search-and-filter flows, product variants, and unfamiliar payment screens.

The evidence is consistent:
- A majority of Indian seniors report finding digital tools confusing, and cite app navigation (not the concept of online shopping) as a core reason.
- This group is highly active in messaging apps (WhatsApp, Telegram) but tends to avoid shopping and payment apps.
- Ease of navigation is a primary driver of whether older users adopt a technology at all.

The result: people who would benefit most from grocery delivery are the least able to use it independently — and end up depending on a family member to order for them.

---

## 3. Goals and objectives

**Primary goal:** Let a non-app-fluent user place a grocery order independently, using only voice/text in a familiar chat app.

**Objectives:**
1. Accept a grocery request as a voice note or text, in the user's own language.
2. Correctly interpret the request and match it to real products.
3. Add the items to the user's real cart with no app navigation required.
4. Keep the user informed and in control at every step — especially payment.

**Non-goals (explicitly out of scope for v1):**
- Completing checkout or handling payment (intentional — see §7).
- Acting as a general-purpose assistant or chatbot.
- Replacing grocery apps for app-fluent users.

---

## 4. Target users

**Primary persona — "Independent Senior"**
An older adult who uses WhatsApp/Telegram daily to stay in touch with family. Comfortable sending voice notes; uncomfortable navigating shopping apps and payment flows. Wants to order groceries without asking their children to do it for them. Values independence and dignity.

**Secondary persona — "Convenience-seeker"**
Any user who would rather speak a quick list than tap through a store app — e.g., someone busy, or ordering hands-free.

---

## 5. User stories

- As a senior user, I want to **speak my grocery list in my own language** so I don't have to type or navigate an app.
- As a user, I want to **see exactly what was added and what wasn't**, so I can trust the result.
- As a user, I want to **review and pay myself**, so I stay in control of my money.
- As a user, I want the agent to **pick sensible products**, so I don't get sponsored or oversized items I didn't intend.
- As a user, I want a **clear message when it can't understand me**, so I'm never left wondering if it worked.

---

## 6. Functional requirements

| # | Requirement | Priority |
|---|---|---|
| FR-1 | Accept voice and text input via a chat interface (Telegram for v1). | Must |
| FR-2 | Transcribe voice to text, supporting English, Hindi, Hinglish, and Tamil. | Must |
| FR-3 | Extract grocery items from natural language and normalise names to English for search. | Must |
| FR-4 | Search the live Swiggy Instamart catalogue for each item. | Must |
| FR-5 | Select one appropriate product per item — exclude sponsored/ad results and avoid bulk packs. | Must |
| FR-6 | Add the selected items to the user's real Swiggy cart. | Must |
| FR-7 | Return an itemised confirmation listing what was added and what could not be found. | Must |
| FR-8 | When no grocery items are detected, reply with a helpful prompt rather than failing silently. | Must |
| FR-9 | Never initiate checkout or payment. | Must |
| FR-10 | Handle mixed availability gracefully (add what's available, flag the rest). | Must |

---

## 7. Key design decisions & rationale

**Cart injection, not checkout.**
The agent fills the cart but never pays. Payment is where older users hesitate most and trust breaks down. Keeping a human in the loop for checkout makes the agent feel safe and keeps the user in control. *(This is a deliberate trust decision, not a technical limitation.)*

**Curated product selection.**
When multiple products match an item, the agent applies simple heuristics — skip sponsored results, prefer standard pack sizes — so it behaves like a considerate human shopper rather than picking the first or most-promoted result.

**Language-agnostic input, normalised internally.**
Users speak however is natural to them; the system normalises item names to English before searching, keeping catalogue matching reliable without asking the user to change their behaviour.

**Telegram for prototyping; WhatsApp for scale.**
Telegram's bot platform is free and requires no approval, making it ideal to validate the concept quickly. WhatsApp — where the target users actually are — is the intended production channel, at the cost of approval and per-message fees. A deliberate build-fast-then-scale trade-off.

---

## 8. Success metrics

| Metric | Target (indicative) |
|---|---|
| Order completion — user speaks a list and correct items appear in cart | ≥ 90% of well-formed requests |
| Language coverage — correct interpretation across supported languages | English, Hindi, Hinglish, Tamil |
| Transparency — user always receives a clear outcome (added / not found / didn't understand) | 100% of requests get a reply |
| Trust — no order is ever placed or paid without explicit user action | 100% (hard constraint) |

*(As a prototype, these are validation targets rather than production analytics.)*

---

## 9. Assumptions & dependencies

- The user has a Swiggy account with a saved delivery address.
- The serving store (dark store) is open and stocking items at order time.
- Access to Swiggy's catalogue and cart is available via its MCP interface.
- Speech-to-text and language models are reachable via API.

---

## 10. Known limitations & roadmap

**Current limitations:**
- Runs on demand rather than fully hands-off (polling-based; a production build would use webhooks or a queue).
- Product matching can occasionally return a loosely-related substitute for an unavailable item.
- No dedicated messaging yet for store-closed or expired-access states.

**Roadmap (v1.1+):**
- Always-on, event-driven trigger for true hands-free use.
- Stricter relevance checking on product matches.
- Graceful handling of store-closed and token-expiry cases.
- WhatsApp channel to reach the primary user segment at scale.

---

## 11. Open questions

- What is the best way to build payment trust for users who *are* willing to complete checkout in-chat later?
- Should the agent learn a user's product preferences over time (e.g., preferred brands)?
- How should reordering ("same as last week") work, and where should that history live?

---

*PantryPilot is a working prototype built to explore interface-to-user fit, agentic tool-use, and AI product thinking. It is a personal project and is not affiliated with Swiggy.*
