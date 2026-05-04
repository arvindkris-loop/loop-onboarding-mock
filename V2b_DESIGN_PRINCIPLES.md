# V2b Onboarding UX — Design Principles

> For Hari Shekar & team. This explains the *why* behind every V2b design choice.

---

## Core Thesis

Onboarding is the first product experience a customer has after signing. If it feels like a form, it feels like work. If it feels like a conversation, it feels like service. V2b is built as a guided conversation, not a portal.

---

## 1. Conversation Over Interface

**What we did:** The entire flow is a chat thread — bot messages, user replies, inline cards. No dashboards, no sidebars, no tabs.

**Why:** Our customers onboard across devices — some on desktop, many on mobile, some via SMS links. A conversational UI degrades gracefully across all channels (web chat, SMS/WhatsApp, Slack). A form-based portal doesn't. Chat also creates a natural turn-taking rhythm: we ask one thing, they do one thing. This eliminates the "wall of fields" overwhelm that causes drop-off in traditional onboarding portals.

**Constraint enforced:** Every bot message must either inform or ask for exactly one decision. No message does both.

---

## 2. Bounded Choices (Max 3 Actions Per Step)

**What we did:** No step in either track asks for more than 3 discrete actions. Most steps require just 1 (tap to confirm).

**Why:** Cognitive load research shows that beyond 3 choices, decision quality drops and abandonment rises. The admin track's hardest step (adding a user on a delivery platform) requires: open portal, navigate to users, paste email + select role — exactly 3. The CFO track's hardest step (payment) requires: choose method, enter details, confirm — exactly 3. Everything else is 1 action (confirm/copy/tap).

**How we enforce it:** If a step can't be done in 3 actions, we split it into sub-steps. If a sub-step still needs 4+, we redesign.

---

## 3. Customer Language Only

**What we did:** Removed every instance of internal terminology — Closed Won, QC, BigQuery, Zenskar, PandaDoc pipeline, 80% threshold, CS Kick-off, Opportunity ID.

**Why:** Internal process steps are invisible to the customer and should stay that way. "Closed Won" means nothing to a restaurant operator. "We verify your connections" does. The customer journey is 5 steps they can understand:

```
Contract Signed → Account Setup → Verification → Meet Your CS Manager → Product Onboarding
```

Not 9 internal stages. The internal 9-step lifecycle still exists — it drives our backend logic and SF stages — but the customer never sees it. Their mental model is simpler and that's by design.

**Rule:** Before any text ships, ask: "Would a restaurant CFO understand this without context?" If no, rewrite.

---

## 4. Pre-Filled Trust, Not Data Entry

**What we did:** Billing entity, products, pricing, locations, AP contact — all shown as pre-filled cards from the signed contract. The customer confirms or flags, never types from scratch.

**Why:** Data entry is the #1 onboarding friction point. Every field a customer has to fill is a chance to abandon. Since we already have their contract data, showing it pre-filled does two things: (a) eliminates typing, (b) builds trust — "they already know who we are." The source label ("Pre-filled from your signed contract") reinforces this without exposing internal systems.

**The edit path is intentionally lightweight:** tap "Something needs updating" → pick field → type correction → flagged for billing team. The customer never has to re-enter everything.

---

## 5. Forward Momentum (No Dead Ends)

**What we did:** Every single step ends with a "Next:" preview. Every card tells you what's coming. Errors don't stop the flow — they show how to fix and what happens after.

**Why:** The #1 anxiety in any multi-step process is "how much more?" and "what if I mess up?" Forward previews eliminate both. The user always knows: what they just did, what's next, and how long it'll take. This is why:

- Copy email step says: *"Next: you'll add this on DoorDash"*
- Verify step says: *"Next: I'll check automatically (~10 sec)"*
- Entity confirmation says: *"Next: billing structure, then products & pricing"*
- Custom billing grouping says: *"Meanwhile, you can continue — grouping doesn't block this"*

Even errors maintain forward momentum. The structured error pattern — **What happened → How to fix (1-3 steps) → After you fix this** — turns failures into guided recovery, not dead ends.

---

## 6. Parallel Tracks with Cross-Awareness

**What we did:** Admin and CFO get separate flows. Each track mentions the other person by name and what they're handling.

**Why:** In mid-market accounts, the person connecting platforms (tech admin) is never the person setting up billing (CFO/AP). Forcing both through the same flow creates confusion and delays. Splitting them means:

- Each person sees only what's relevant to their role (~8-12 min, not 20+)
- Neither blocks the other
- Both know the other track exists: *"Meanwhile, Michael Torres is handling billing setup separately"*

After the admin finishes, the flow explicitly says: *"Next up, we'll reach out to the billing contact."* No ambiguity about who does what.

---

## 7. Transparency Without Exposure

**What we did:** Live verification checklists animate in real-time (API check → role verify → store count). Running store counters update after each platform. Billing structure choice is shown in the summary.

**Why:** Customers hate black boxes. "Verifying..." with a spinner creates anxiety. A 3-item checklist with progressive checkmarks creates confidence — they can see exactly what's being checked and what passed. The store counter after each platform ("52/141 stores, 89 more to hit the target") gives tangible progress.

But transparency has limits. We show *what* is happening, not *how* or *where*. The customer sees "Stripe confirmed → Payment method valid → Invoicing activated." They don't see "Stripe → Zenskar API → BQ write." Implementation details aren't transparency — they're noise.

---

## 8. Billing Entity Structure as a First-Class Decision

**What we did:** After confirming entity details, we ask how to structure billing across locations: all-as-one, per-location, or custom grouping (with a finance call).

**Why:** This was previously handled ad-hoc post-onboarding, causing billing delays and rework. Surfacing it early — as a simple 3-option choice — captures the decision when the CFO is already engaged. The custom path (finance schedules a call to map groups) doesn't block the rest of the flow. Products and payment continue in parallel.

---

## Summary: The V2b UX Checklist

| Principle | Test |
|-----------|------|
| Conversation over interface | Is every interaction a message + reply? |
| Bounded choices | Does any step require >3 actions? |
| Customer language | Would a restaurant operator understand every word? |
| Pre-filled trust | Is the customer confirming, not typing? |
| Forward momentum | Does every step end with "Next:"? |
| Parallel tracks | Can admin and CFO work independently? |
| Transparency without exposure | Can the customer see what's happening without seeing how? |
| Billing structure captured | Is entity grouping decided before payment? |
