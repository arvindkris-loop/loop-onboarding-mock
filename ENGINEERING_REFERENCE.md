# Loop Self-Serve Onboarding V2b — Engineering Reference

> **Mock location:** `Loop_Onboarding_V2b.html` (single-file, no dependencies)
> **Spec:** `Loop_Onboarding_V2b_Spec.md`
> **Last updated:** May 4, 2026

---

## 1. What This Is

An interactive chat-first mock of the self-serve onboarding flow that a customer sees after signing their contract. It demonstrates the full experience from contract confirmation through platform access and billing setup, ending with a handoff to CS.

**This is a clickable prototype, not production code.** It's meant to align engineering, product, and design on the target UX before build.

---

## 2. Product Architecture (Current)

All pricing is **per-location/month, fixed**. No variable fees. No % success fees.

| Product | PPL | What It Does |
|---------|-----|-------------|
| **Measure** | $100 | P&L reporting, revenue finance, JE push |
| **Protect** | $100 | Operations alerts (SPR/SOTU/86d/downtime/digests), Recover/Restore/DWT dispute workflows |
| **Grow** | $200 | Marketing analytics, campaign ops & plan generation |
| **Connect** | $99 | Unlimited connectors beyond 3PD |
| **AI BI** | $99 | AI + analytics across all modules (without module functionality) |

### Per-Product Breakdown

**Measure ($100/loc/mo)**
- Datastreams: 3PD Finance, Revenue Finance
- Alerts & Reports: P&L Report
- Workflows: JE Push
- Connectors: 3PD Finance, Olo, PoS, Accounting, Bank

**Protect ($100/loc/mo)**
- Datastreams: 3PD Operations
- Alerts & Reports: SPR, SOTU, 86d, Store Downtime, Digests, Day Part, Delivery Intel
- Workflows: Recover, Restore, DWT Disputes
- Connectors: CCTV, PoS, Olo, 3PD
- Includes **Samantha AI assistant** when Protect >= $100 (otherwise +$25)

**Grow ($200/loc/mo)**
- Datastreams: 3PD Marketing
- Alerts & Reports: Marketing Performance Report
- Workflows: Campaign Ops & Plan Gen
- Connectors: 3PD Marketing

### Add-on Rules

| Scenario | Add-on Price |
|----------|-------------|
| Has Protect, wants finance & marketing analytics | +$100 |
| Has Grow, wants finance (3PD finance, revenue analytics, no recon) | +$50 |
| Samantha AI when Protect < $100 | +$25 |

### Key Change from Old Model
- **Old:** Guard ($35), Balance ($30), Re-engage ($25), Recover (25% success fee) — $90 bundle PPL + variable
- **New:** Measure ($100), Protect ($100), Grow ($200), Connect ($99), AI BI ($99) — all fixed PPL, no variable
- **Recover** is now a workflow inside Protect, not a standalone product

---

## 3. Demo Customer Profile

```
Company:        Acme Restaurants LLC
Segment:        Mid-Market
Locations:      52 unique restaurants

Contracted:     Protect ($100) + Measure ($100)
Total PPL:      $200/loc/mo
MRR:            $10,400/mo
ACV:            $124,800

Admin:          Jamie Park (jamie.park@acmerestaurants.com)
Contract Signee: Michael Torres
AP Contact:     Sarah Chen (sarah.chen@acmerestaurants.com)
Billing Address: 1234 Main St, Chicago, IL 60601
Payment Terms:  Net 30
Term:           12 months (auto-renew)
```

### Platform Distribution (52 unique locations, not all on every platform)

| Platform | Stores | Required Role |
|----------|--------|---------------|
| DoorDash | 52 | Business Admin |
| Uber Eats | 48 | Full Access |
| Grubhub | 41 | Admin (NOT Basic) |
| **Total** | **141** | |

### Access Threshold
- 80% of 141 = **113 stores** needed for Closed Won
- Threshold is across all platforms combined, not per-platform

---

## 4. Onboarding Flow Architecture

### 9-Step Customer Lifecycle

```
[1] Opportunity ID          ✓ done
[2] Pricing + Locations     ✓ done
[3] Closure Commitment      ✓ done
[4] Contract Signed         ✓ done
[5] Self-Serve Onboarding   ← CURRENT (this flow)
[6] Closed Won              auto at 80% store access
[7] QC Completed            1-2 business days
[8] CS Kick-off             CS manager reaches out
[9] Product Onboarding      Measure, Protect, Grow, Connect, AI BI
```

### Step 5 Splits Into Two Parallel Tracks

```
                    ┌─────────────────────────────────────┐
                    │   Step 5: Self-Serve Onboarding     │
                    └──────────────┬──────────────────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
          ┌────────▼────────┐          ┌─────────▼────────┐
          │  Track A: Admin │          │  Track B: CFO    │
          │  Access & Tech  │          │  Business & Bill │
          │  ~12 min        │          │  ~8 min          │
          └────────┬────────┘          └─────────┬────────┘
                   │                             │
          ┌────────▼────────┐          ┌─────────▼────────┐
          │ Per Platform:   │          │ 1. Verify Entity │
          │  1. Copy email  │          │ 2. Confirm Terms │
          │  2. Add user    │          │ 3. Payment Setup │
          │  3. Verify      │          └─────────┬────────┘
          │ x3 platforms    │                    │
          └────────┬────────┘                    │
                   │                             │
                   └──────────────┬──────────────┘
                                  │
                    ┌─────────────▼───────────────┐
                    │  Summary → Next Steps →     │
                    │  Celebration → Free Chat    │
                    └─────────────────────────────┘
```

### Admin Track Detail (per platform)

| Step | Max Actions | What Happens |
|------|-------------|-------------|
| Copy email | 1 (tap to copy) | Shows `onboarding+acme@loopkitchen.com`, previews next step |
| Add user | 3 (open portal → navigate → paste+role) | Platform-specific instructions with role warnings |
| Verify | 1 (confirm) | Live animated checklist: API check → role verify → store count |

After each platform: running store counter updates, shows progress toward 80% threshold.

**Simulated error:** Uber Eats returns role error on first attempt → structured error card → fix steps → re-verify → success.

### CFO Track Detail

| Step | Max Actions | What Happens |
|------|-------------|-------------|
| Verify billing entity | 1 (confirm or flag) | Pre-filled: entity, address, segment, AP contact. Edit triggers flagging flow. |
| Confirm products & pricing | 1 (confirm or dispute) | Per-product detail cards with datastreams/alerts/workflows/connectors. Dispute routes to AM. |
| Payment method | 2-3 (select method → complete) | Stripe (CC/ACH) or Wire. Live verification checklist animation. |

---

## 5. Design Rules

These are enforced throughout the flow:

1. **Chat-first, mobile-first** — 480px max-width, `100dvh`, safe-area insets. Everything is conversational.
2. **Max 3 actions per step** — No step requires more than 3 discrete user actions.
3. **Every step ends with "Next:" preview** — User always knows what's coming. No black box.
4. **Structured error pattern** — Every error shows: What happened → How to fix (1-3 numbered steps) → After you fix this.
5. **No internal system names in customer UI** — BigQuery, Zenskar, PandaDoc pipeline details are never shown. Data is "pre-filled from your signed contract."
6. **Cross-track awareness** — Each track mentions the other person handling the other track.

---

## 6. State Model (from the mock)

```javascript
S = {
  role: 'admin' | 'cfo',
  phase: 'welcome' | 'lifecycle' | 'role_select' | 'track_overview' |
         'platform' | 'billing' | 'summary' | 'next_steps' | 'done',

  contract: {
    products: [
      { name, ppl, datastreams[], alerts[], workflows[], connectors[], note }
    ],
    totalPPL: 200,        // sum of contracted product PPLs
    locations: 52,
    segment: 'Mid-Market',
    // billing entity, AP contact, admin, signee...
    catalog: { ... }      // full product catalog for reference
  },

  // Admin track
  platIdx: 0,             // current platform index (0-2)
  platStep: 0,            // current step within platform (0-2)
  platforms: { dd, ue, gh }, // each has: name, status, email, role, stores
  accessStores: 0,        // running verified total
  get totalPlatformStores() { ... }, // 141 (sum across platforms)

  // CFO track
  billingStep: 0,         // 1-3

  // Flags
  _ueRetried: false,
  _billingEntityEdited: false,
  _paymentCompleted: false
}
```

---

## 7. Key Formulas

```
MRR             = totalPPL × locations
                = $200 × 52 = $10,400

ACV             = MRR × 12
                = $10,400 × 12 = $124,800

Access threshold = totalPlatformStores × 0.8
                 = 141 × 0.8 = 113 stores

Access %        = accessStores / totalPlatformStores
                (NOT accessStores / locations)
```

**Important:** `totalPlatformStores` (141) ≠ `locations` (52). A location can appear on multiple platforms. The 80% threshold applies to the total platform-store count, not unique locations.

---

## 8. UI Component Inventory

| Component | CSS Class/ID | Purpose |
|-----------|-------------|---------|
| Top bar | `.topbar` | Progress steps (4 segments), time estimate, journey button, track label |
| Chat area | `#chat` | Scrollable message container |
| Bot message | `.msg.bot` | Card-based messages with `.card` variants |
| User message | `.msg.user` | Right-aligned blue bubbles |
| Quick replies | `#replies` | Button row for single-tap responses |
| Free text input | `#userInput` + `#sendBtn` | Text input for name entry and free chat |
| Journey overlay | `#journeyOverlay` | Full 9-step lifecycle map |
| Progress FAB | `#progFab` | Floating card showing platform or billing progress |
| Celebration | `#celebration` | Full-screen completion overlay |
| Typing indicator | `.typing` | Three-dot animation during bot "thinking" |

### Card Variants

| Class | Use |
|-------|-----|
| `.card.highlight` | Primary info cards (products, pricing, entity) |
| `.card.success` | Confirmations, completions |
| `.card.warning` | Errors, disputes, flagged items |
| `.card.navy-card` | Structural/navigational info |

### Platform Pills

| Class | Color |
|-------|-------|
| `.plat-pill.dd` | DoorDash red (#ff3008) |
| `.plat-pill.ue` | Uber Eats green (#06c167) |
| `.plat-pill.gh` | Grubhub red (#e3342f) |

---

## 9. Channel Adaptability

The flow is designed to degrade across channels:

| Feature | Web Chat | SMS/WhatsApp | Slack/Teams |
|---------|----------|--------------|-------------|
| Lifecycle card | Rich HTML stepper | "Step 5 of 9" text | Adaptive card |
| Role selection | Tap buttons | "Reply A or B" | Button blocks |
| Copy email | Tap to copy | Plain text | Code block |
| Pre-filled data | Card with rows | Formatted text list | Key-value fields |
| Payment link | Embedded button | URL link | Button with URL |
| Verification | Animated checklist | "Checking... Done" | Threaded updates |
| Journey map | Overlay | "Reply MAP" | Slash command |

---

## 10. What's NOT in the Mock (Production Gaps)

| Area | Mock | Production Needs |
|------|------|-----------------|
| Auth | None | Firebase auth, role-based access per contract |
| Data | Hardcoded state object | PandaDoc webhook → contract data API |
| Platform verification | Simulated delays | Real API calls to DD/UE/GH partner portals |
| Payment | Simulated Stripe card | Real Stripe Checkout session via billing service |
| Persistence | In-memory JS state | Session state in DB, resume capability |
| Notifications | None | Email/SMS reminders for incomplete onboarding |
| Analytics | None | Hotjar/GA for funnel tracking (TODO) |
| Multi-user | Single user demo | Both admin + CFO tracking against same contract |
| Error handling | One simulated error | Real error detection per platform API |

---

## 11. File Structure

```
loop-onboarding-v2/
├── Loop_Onboarding_V2b.html        ← Current interactive mock
├── Loop_Onboarding_V2b_Spec.md     ← Detailed spec (source doc mapping)
├── Loop_Onboarding_V2.html         ← V2a (access track only, preserved)
├── Loop_Onboarding_V2_Spec.md      ← V2a spec (preserved)
└── ENGINEERING_REFERENCE.md         ← This file
```

---

## 12. Quick Reference: SF Stage Mapping

| SF Stage | Trigger | Onboarding Step |
|----------|---------|-----------------|
| Proposal | — | 1-2 |
| Contract Signed | PandaDoc signed | 4 |
| Access | Self-serve onboarding started | 5 |
| Closed Won | 80% stores verified across platforms | 6 |

*Effective Apr 7, 2026. See `project_sf_stage_changes.md` for details.*
