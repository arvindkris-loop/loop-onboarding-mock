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

---
---

# V2b Flow Walkthrough — Every Step in Detail

> This section maps the exact sequence of screens, messages, and decision points a customer encounters. Use it to spec engineering tickets, QA test plans, or design reviews.

---

## Phase 1: Welcome & Context Setting

**Who:** Both admin and CFO see this phase identically before routing.

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| 1.1 | Greeting | "Welcome to Loop self-serve onboarding! I'll guide you through everything — step by step, no guesswork." | None (read) |
| 1.2 | Journey card | 5-step visual stepper. Step 1 (Contract Signed) has a green check. Step 2 (Account Setup) is highlighted amber with "You are here." Steps 3-5 are grayed out. Footer: "Tap the menu icon anytime to see this map." | None (read) |
| 1.3 | Contract data card | "Here's what we have on file:" — card showing contracted products (e.g., Protect + Measure), total PPL ($200/loc/mo), MRR ($10,400/mo), locations (52), segment (Mid-Market). Footer: "All contract data received and verified." | None (read) |
| 1.4 | Track explanation | "Two things need to happen now — they run in parallel:" — navy card with Track A (Access & Tech → tech admin name) and Track B (Business & Billing → contract signee name). | None (read) |
| 1.5 | Role selection | "Which one are you here for?" | **Tap 1 of 2 buttons:** "I'm the Tech Admin" or "I'm the Billing Contact" |

**Routing:** Selection determines which track loads. The other track's person is referenced by name throughout.

---

## Phase 2A: Admin Track — Platform Access

**Who:** Tech admin (e.g., Jamie Park). **Duration:** ~12 min. **Structure:** 3 platforms x 3 steps each.

### Track Overview (shown once)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| 2A.0a | Track card | "Your Track: Access & Tech Integration" — 3 platforms listed (DoorDash, Uber Eats, Grubhub), ~3 min each, max 3 actions per step. Goal: connect 113+ stores to get up and running. | None (read) |
| 2A.0b | Cross-track note | "Meanwhile, Michael Torres will get a separate link for billing setup. You don't need to handle that." | None (read) |
| 2A.0c | Ready prompt | "Next: We'll start with DoorDash. Ready?" | **Tap:** "Let's start!" or "How does verification work?" (explainer then continue) |

### Per-Platform Loop (repeats for DoorDash → Uber Eats → Grubhub)

**Step 1: Copy Email** (1 action)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| P.1a | Instruction | "Step 1 of 3: Copy this email address." | None (read) |
| P.1b | Copy field | Large tappable field showing `onboarding+acme@loopkitchen.com` with "TAP TO COPY" button. Below: "Next: you'll add this as a user on [Platform]." | **Tap to copy** (1 action). Field turns green, shows "COPIED!" |
| P.1c | Advance | "Got it! Now let's add it on [Platform]." | **Tap:** "Next" |

**Step 2: Add User on Platform** (3 actions max)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| P.2a | Instruction card | Numbered steps: (1) Open [Platform] portal, (2) Go to Users/Access section, (3) Paste email and select [required role]. Platform-specific role warning (e.g., "Must be Business Admin, not Store Manager"). | **3 actions:** open portal, navigate, paste + select role |
| P.2b | Confirmation | "Done adding the user? I'll verify automatically — takes about 10 seconds." | **Tap:** "I've added it" or "I need help" (shows troubleshooting) |

**Step 3: Verify** (1 action)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| P.3a | Live checklist | Animated 3-item checklist: (1) Checking [Platform] API... (2) Verifying role... (3) Counting stores... Items check off progressively with ~1s delays. | **Watch** (no action during animation) |
| P.3b | Result — success | Card turns green: "API connected, [Role] confirmed, [N] stores found." Running total updates: "[X]/141 stores ([Y]%). [Z] more to hit the target." | **Tap:** "Next platform" or "Continue" |
| P.3c | Result — error | **Structured error card:** What happened (e.g., "Role is set to Basic instead of Admin"). How to fix: 1-2-3 numbered steps. After you fix this: "I'll re-verify. Then we move to [next platform]." | **Tap:** "I've fixed it" (re-triggers verification) |

**Simulated error (demo only):** Uber Eats returns a role error on first attempt. Customer sees fix steps, taps "I've fixed it," verification succeeds on retry.

### After Each Platform

Bot shows: platform pill with checkmark, stores added, running total with progress bar, and preview of next platform. Progress FAB (floating card) updates with per-platform status.

### After All 3 Platforms

Bot shows: "Next up, we'll reach out to [billing contact name] to complete billing — entity confirmation, products & pricing review, and payment setup." Navy card reinforces this runs in parallel.

---

## Phase 2B: CFO Track — Billing Setup

**Who:** Billing contact / CFO (e.g., Michael Torres). **Duration:** ~8 min. **Structure:** 3 steps + 1 sub-step (billing structure).

### Track Overview (shown once)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| 2B.0a | Track card | "Your Track: Billing Setup" — 3 steps listed (Verify entity, Confirm products, Set up payment). ~8 min, most info pre-filled. | None (read) |
| 2B.0b | Cross-track note | "Meanwhile, Jamie Park is handling platform access connections. You don't need to worry about that." | None (read) |
| 2B.0c | Ready prompt | "Next: We'll verify your billing entity. Most of this is pre-filled — you just need to confirm." | **Tap:** "Let's start!" or "What do I need to have ready?" (answer: just payment details) |

### Step 1: Verify Billing Entity (1 action)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.1a | Pre-filled card | Entity Name, Address, Segment, AP Contact, AP Email — all populated from contract. Footer: "Pre-filled from your signed contract." | None (read) |
| B.1b | Confirm prompt | "Next step after this: billing structure, then products & pricing." | **Tap:** "Looks correct!" or "Something needs updating" |

**If editing:** Customer picks which field → types correction → flagged for billing team (1 business day) → flow continues. Edit doesn't block.

### Step 1b: Billing Entity Structure (1 action)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.1c | Structure options | "How would you like your 52 locations organized for billing?" — card with 3 options: (A) All locations under one entity — single invoice. (B) Each location as its own entity — 52 separate invoices. (C) Custom grouping — regions, brands, or franchisees. | **Tap 1 of 3 buttons** |

**Path A (Single):** Confirmation card — "All 52 locations billed under Acme Restaurants LLC. One consolidated invoice." → Continue.

**Path B (Per-location):** Confirmation card — "Each location gets its own invoice. All go to AP contact. Billing team sets up within 1 business day." → Continue.

**Path C (Custom):** Warning card with 3 steps — (1) Finance team emails AP contact within 1 day, (2) Quick call to map locations into groups, (3) Entities created & invoicing configured. Key: "You can continue with products & payment — grouping doesn't block that." → Continue.

### Step 2: Confirm Products & Pricing (1 action)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.2a | Products card | Product tags (Protect, Measure). Per-product detail blocks showing: price ($100/loc/mo each), datastreams, alerts & reports, workflows, connectors. Protect card also shows "Includes Samantha AI assistant." Below: Total PPL ($200/loc/mo), locations (52), MRR ($10,400), ACV ($124,800), payment terms, contract term. Footer: "Pre-filled from your signed contract." | None (read) |
| B.2b | Confirm prompt | "Next: set up your payment method so invoicing can begin." | **Tap:** "Confirmed!" or "This doesn't look right" |

**If disputed:** Routed to account manager (1 business day). Customer can still continue to payment — pricing changes apply to future invoices.

### Step 3: Payment Method (2-3 actions)

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.3a | Method selection | "How would you like to pay?" | **Tap:** "Credit Card / ACH" or "Wire Transfer" |

**Stripe path (CC/ACH):**

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.3b | Checkout card | Simulated Stripe secure checkout button. "Opens Stripe's secure payment page." | **Tap:** "Open Secure Checkout" |
| B.3c | Return | "Complete payment on the Stripe page, then come back here." | **Tap:** "I've completed payment" |
| B.3d | Verification | Live 3-item checklist: (1) Stripe confirmed (2) Payment method valid (3) Invoicing activated. Progressive checkmarks. Card turns green. | **Watch** → **Tap:** "See summary" |

**Wire path:**

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| B.3b | Wire card | "Our billing team will email wire instructions to [AP email] within 1 business day." Reference: billing entity name. | None (read) |
| B.3c | Advance | | **Tap:** "Continue" |

---

## Phase 3: Track Summary

**Admin summary card:** Per-platform status (name + pill + checkmark + store count), total stores verified vs. 141, target status (reached or N remaining).

**CFO summary card:** Billing entity (confirmed or update pending), billing structure (single / per-location / custom with call scheduled), products (confirmed), MRR with breakdown, payment method (verified or pending), AP contact.

---

## Phase 4: Next Steps & Handoff

| # | Bot Action | What Customer Sees | Customer Action |
|---|-----------|-------------------|----------------|
| 4.1 | Cross-track status | **Admin sees:** "Next up, we'll reach out to [billing contact] for billing setup." **CFO sees:** "[Admin name] is handling platform access separately." | None (read) |
| 4.2 | What Happens Next | Navy card with 3 steps: (1) We verify everything — 1-2 business days, (2) Your CS manager reaches out — dedicated point of contact, (3) Product onboarding begins — guided setup for contracted products. Footer: "No action needed — we'll reach out when it's time." | None (read) |
| 4.3 | Product preview | "Coming Soon" card listing all 5 products: Measure, Protect, Grow, Connect, AI BI — each with one-line description. Footer: "Same chat-first experience — guided setup, no guesswork." | None (read) |
| 4.4 | Wrap-up | "Looks great!" or "I have a question" | **Tap choice** |

**If "I have a question":** Free-text chat opens. Keyword matching handles common topics (timing, stores, billing, products, contract, access roles). Unmatched questions: "I'll flag this for your CS manager." After Q&A → celebration screen.

---

## Phase 5: Celebration

Full-screen overlay with track-specific headline ("Access Onboarding Complete!" or "Billing Setup Complete!"), subtitle with timeline ("Verification: 1-2 days → CS manager intro → Product onboarding"), and "Back to Dashboard" CTA.

---

## Complete Action Count

| Track | Steps | Total Customer Actions | Time |
|-------|-------|-----------------------|------|
| Admin | 3 platforms x 3 steps + overview + summary | 12-15 taps | ~12 min |
| CFO | 3 steps + structure sub-step + summary | 6-8 taps | ~8 min |
| Shared | Welcome + role select + next steps | 3-4 taps | ~3 min |
