# Loop Self-Serve Onboarding V2b — Spec

## Source Document Mapping

This mock strictly follows the conversation flow defined in the "Self-serve Onboarding.pages" attachment. Every numbered item in the source document maps to a concrete UI element or conversation phase.

### Document Structure → V2b Implementation

| Document Item | V2b Implementation |
|---|---|
| 1. Opportunity ID | Lifecycle card — shown as completed (green check) |
| 2. Pricing + Locs | Lifecycle card — shown as completed |
| 3. Closure commitment | Lifecycle card — shown as completed |
| 4. Contract signed | Lifecycle card — completed; sub-items shown in "Data Received" card |
| 4.1 Products confirmed | Shown in contract data card: "All contract data received and verified ✓" |
| 4.2 Pricing confirmed | Shown in contract data card (merged with 4.1) |
| 4.3 Billing info received | Shown in contract data card (merged with 4.1) |
| 4.4 Admin info received | Shown in contract data card (merged with 4.1) |
| 5. Self-Serve onboarding | **Current step** — highlighted in lifecycle card, drives role routing |
| 5.1 Access & Tech → Admin | **Admin Track** — full interactive platform connect flow |
| 5.2 Business & Billing → CFO | **CFO Track** — full interactive billing setup flow |
| 6. Closed Won | Next Steps card — "automatic once 80% store access verified" |
| 7. QC Completed | Next Steps card — "our team verifies connections (1-2 business days)" |
| 8. CS Kick-off | Next Steps card — "your CS manager reaches out" |
| 9. Self-serve onboarding (products) | "Coming Soon" card with all four modules |
| 9.1 Measure | Product Onboarding card — "P&L reporting, revenue finance & JE push" |
| 9.2 Protect | Product Onboarding card — "Operations alerts, dispute recovery & Samantha AI" |
| 9.3 Grow | Product Onboarding card — "Campaign ops, marketing analytics & plan generation" |
| 9.4 Connect | Product Onboarding card — "Unlimited connectors beyond 3PD platforms" |
| 9.5 AI BI | Product Onboarding card — "AI-powered analytics across all modules" |

### Document Rules → V2b Compliance

| Rule | Implementation |
|---|---|
| Chat-first and mobile-first | Entire flow is conversational. 480px max-width. 100dvh. Safe-area insets. |
| Overall Steps + Time | Lifecycle card (9 steps), progress bar (4 segments), time estimate in top bar, journey map overlay (tap ☰) |
| Clear action per step (max of 3) | Admin: Copy (1 action) → Add user (3 actions) → Verify (1 action). CFO: Confirm entity (1 action) → Confirm terms (1 action) → Pay (3 actions max). |
| Well-guided errors with actionable solution | Every error card has: What happened → How to fix (numbered, ≤3 steps) → After you fix this (next step preview) |
| Next-step expectation (no black box) | Every step ends with "Next:" preview. Verification shows live animated checklist. Pre-filled data shown as "from your signed contract". |

---

## Conversation Flow

### Phase 1: Welcome & Lifecycle Context

1. Bot greets user
2. Shows **lifecycle card** — all 9 steps with current position (step 5 highlighted)
3. Shows **contract data card** — pre-filled data from signed contract (products, pricing, locations, segment)
4. Explains two parallel tracks: Access (Admin) + Billing (CFO)
5. Asks user to identify their role → routes to appropriate track

### Phase 2A: Admin Track — Access & Technology Integration

**Overview:**
- Shows track card: 3 platforms × 3 steps, ~12 min
- Mentions CFO is handling billing separately
- States 80% threshold goal (326 of 407 stores)
- Offers explanation of how verification works

**Per-Platform Flow (DoorDash → Uber Eats → Grubhub):**

| Step | Actions | What User Sees |
|---|---|---|
| 1. Copy Email | 1 action: tap to copy | Copy field with email, "Next: you'll add this on [Platform]" |
| 2. Add User | 3 actions max: open portal → navigate → paste + select role | Numbered instruction card, platform-specific warnings, "Next: I'll verify automatically (~10 sec)" |
| 3. Verify | 1 action: confirm | Live animated checklist (3 items), progressive check marks |

**After Each Platform:**
- Stores added to running total
- Access progress shown: "142/407 stores (35%). Need 326 (80%) for Closed Won."
- Preview of next platform: "Next: Uber Eats. 2 platforms remaining."

**Error Pattern (every error follows this template):**
```
What happened: [specific — e.g., "Role is set to Basic instead of Full Access"]
How to fix: [1-3 numbered steps]
After you fix this: [forward expectation — e.g., "I'll re-verify. Then we move to Grubhub."]
```

**Simulated Error (demo):** Uber Eats returns a role error on first attempt. User sees fix steps, re-verifies, succeeds.

### Phase 2B: CFO Track — Business & Billing

**Overview:**
- Shows track card: 3 steps, ~8 min
- Mentions admin is handling access separately
- Notes most info is pre-filled from contract

**Step 1: Verify Billing Entity** (1 action — confirm or flag)
- Pre-filled card: Entity name, address, segment, AP contact, AP email
- Source shown: "Pre-filled from your signed contract"
- If incorrect: guided edit flow (select field → type correction → flagged for billing team)
- "Next: confirm products & pricing"

**Step 2: Confirm Products & Pricing** (1 action — confirm or dispute)
- Pre-filled card: Product tags, per-product pricing, bundle PPL, locations, estimated monthly, payment terms, contract term
- If disputed: routed to account manager, can still continue to payment
- "Next: set up payment method"

**Step 3: Payment Method** (2-3 actions)
- Options: Credit Card/ACH (Stripe) or Wire Transfer
- Stripe path: Open checkout → enter details → confirm back
- Wire path: instructions sent to AP contact email
- Live verification checklist (Stripe confirmation → validation → invoicing activation)
- Error recovery: alternative methods, retry, skip

### Phase 3: Summary (Track-Specific)

**Admin Summary:**
- Per-platform status with store counts
- Total stores verified vs. 407 contracted
- 80% threshold status (reached or remaining)

**CFO Summary:**
- Billing entity status (confirmed or update pending)
- Products confirmed
- Monthly estimate
- Payment method status (verified or pending)

### Phase 4: Next Steps (Shared)

- Cross-track awareness: "Meanwhile, [other person] is handling [their track]"
- Steps 6-8 card: Closed Won (auto) → QC (1-2 days) → CS Kick-off
- Step 9 preview: Agents, Marketing, Finance, Protect
- "No action needed until CS kick-off"
- Free-chat for questions (keyword-matched + CS escalation fallback)

### Phase 5: Celebration

- Track-specific message
- QC → CS → Product onboarding timeline
- "Back to Dashboard" CTA

---

## Data Model

### Contract Data (Pre-Filled from Signed Contract)

```
Signed: Apr 28, 2026
Segment: Mid-Market
Locations: 52

Contracted Products:
  Protect: $100/loc/mo
    Datastreams: 3PD Operations
    Alerts & Reports: SPR, SOTU, 86d, Store Downtime, Digests, Day Part, Delivery Intel
    Workflows: Recover, Restore, DWT Disputes
    Connectors: CCTV, PoS, Olo, 3PD
    Includes: Samantha AI assistant (included when Protect >= $100)

  Measure: $100/loc/mo
    Datastreams: 3PD Finance, Revenue Finance
    Alerts & Reports: P&L Report
    Workflows: JE Push
    Connectors: 3PD Finance, Olo, PoS, Accounting, Bank

Total PPL: $200/location/month (Protect + Measure)
MRR: $10,400/month ($200 x 52 locations)
ACV: $124,800
Payment Terms: Net 30
Term: 12 months (auto-renew)
Billing Entity: Acme Restaurants LLC
Address: 1234 Main St, Chicago, IL 60601
AP Contact: Sarah Chen (sarah.chen@acmerestaurants.com)
Admin: Jamie Park (jamie.park@acmerestaurants.com)
Contract Signee: Michael Torres
```

**Product catalog (full):**
| Product | Price | Key Capabilities |
|---|---|---|
| Measure | $100/loc/mo | P&L reporting, revenue finance, JE push, full finance connectors |
| Protect | $100/loc/mo | Operations alerts (SPR/SOTU/86d/downtime), Recover/Restore/DWT workflows, CCTV/PoS/Olo/3PD connectors |
| Grow | $200/loc/mo | Marketing analytics, campaign ops & plan gen, 3PD marketing connectors |
| Connect | $99/loc/mo | Unlimited connectors outside of 3PD |
| AI BI | $99/loc/mo | AI-powered analytics across all modules (without module functionality) |

**Add-ons:** Has Protect + wants finance & marketing analytics = +$100. Has Grow + wants finance = +$50 (includes 3PD finance, all revenue analytics, no recon). Samantha AI assistant included when Protect >= $100, otherwise +$25. **No % success fee** — Recover is a workflow within Protect, not separately billed.

### Platform Data

| Platform | Email | Role | Stores |
|---|---|---|---|
| DoorDash | onboarding+acme@loopkitchen.com | Business Admin | 52 |
| Uber Eats | onboarding+acme@loopkitchen.com | Full Access | 48 |
| Grubhub | onboarding+acme@loopkitchen.com | Admin (NOT Basic) | 41 |

52 unique restaurant locations; not all locations present on every platform (common in mid-market).

### Access Threshold

- Total stores across platforms: 141 (52 + 48 + 41)
- 80% threshold: 113 stores
- Closed Won triggers automatically when threshold is met across all 3 platforms
- Running store counter updates after each platform verification

---

## Augmented Insights (from existing knowledge)

1. **80% threshold for Closed Won** — from SF stage changes (effective Apr 7, 2026). "Access" = 80% of contracted stores across all 3 platforms.

2. **Pre-filled contract data** — Contract data is shown as "pre-filled from your signed contract" without exposing internal system routing (BigQuery, Zenskar). Customer sees confirmation that their data arrived correctly.

3. **Payment processing** — Payment links to Stripe. Billing entity IDs follow the pattern BIL-{segment}-{country}-{industry}-{seq}-{date}. Internal billing partner (Zenskar) is not exposed to the customer.

4. **Products & Pricing** — New product architecture: Measure, Protect, Grow, Connect, AI BI. All pricing is per-location/month with no variable fees. Recover is now a workflow within Protect (not a standalone product with success fees). Protect includes Samantha AI assistant when >= $100/loc. Demo customer uses Protect ($100) + Measure ($100) = $200/loc/mo for 52 locations.

5. **Entity hierarchy awareness** — The data model supports Chain → Brand → Location and Customer → Contract → Billing hierarchies. For franchise groups with parent-child relationships, the billing entity may represent the parent.

6. **Pre-access backlog context** — The self-serve onboarding flow is designed to reduce the time-to-access that previously caused 42 accounts / $500K to get stuck between contract signing and activation.

---

## UI Components (New in V2b vs V2a)

| Component | Purpose |
|---|---|
| **Journey map overlay** | Full 9-step lifecycle view, toggleable from top bar |
| **Track label** | Top bar indicator showing "Access & Tech" or "Business & Billing" |
| **Contract data card** | Pre-filled data from signed contract (no internal system references) |
| **Data rows** | Label-value pairs for billing/contract data display |
| **Product tags** | Colored pill badges for contracted products |
| **Access bar** | Progress bar with 80% threshold marker for store verification |
| **Navy card** | Dark-themed card for structural info (tracks, next steps) |
| **Billing entity card** | Pre-filled entity details with confirm/edit flow |
| **Stripe checkout card** | Simulated secure payment link with alternatives |
| **Payment verification checklist** | Stripe confirmation → validation → invoicing activation animation |

---

## Channel Adaptability

| Feature | Web Chat | SMS/WhatsApp | Slack/Teams |
|---|---|---|---|
| Lifecycle card | Rich HTML stepper | "You're at step 5 of 9" text summary | Adaptive card |
| Role selection | Tap buttons | "Reply A for Admin, B for Billing" | Button blocks |
| Copy email | Tap to copy | Plain text to copy | Code block |
| Pre-filled data | Data row cards ("Pre-filled from your signed contract") | Formatted text list | Key-value fields |
| Payment link | Embedded button | URL link | Button with URL |
| Verification | Animated checklist | Status updates ("Checking... ✓ Done") | Threaded updates |
| Journey map | Overlay with stepper | "Reply MAP to see your journey" | Slash command |

---

## Differences: V2a → V2b

| Aspect | V2a | V2b |
|---|---|---|
| Scope | Access track only | Full lifecycle — both tracks |
| Role routing | None (assumes admin) | Asks role, routes to admin or CFO track |
| Lifecycle context | Not shown | Full 9-step journey card + overlay |
| Contract data | Not shown | Pre-piped data displayed with source |
| Billing track | Not implemented | Full 3-step flow (entity, terms, payment) |
| Cross-track awareness | Not shown | Mentions other person handling other track |
| 80% threshold | Not shown | Running store counter + access bar with threshold |
| Error pattern | Basic | Structured: What happened + How to fix + After this |
| Next-step preview | Partial | Every step ends with forward expectation |
| Post-access products | Listed | Positioned as step 9 in lifecycle with descriptions |
