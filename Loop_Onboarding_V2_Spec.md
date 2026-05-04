# Loop Self-Serve Onboarding V2 — Spec

## Design Philosophy

V2 evolves the V1a three-column desktop layout into a **chat-first, mobile-first** experience. The entire onboarding is driven through a conversational interface — no forms, no sidebars, no complex navigation. Every interaction is a tap or a short text input.

### Core Principles (carried from V1a)
1. **Progressive disclosure** — show only what's needed right now
2. **State transparency** — user always knows where they are and what's next
3. **Reassurance** — time estimates, progress bars, clear error recovery
4. **Conversational guidance** — warm, prescriptive tone

### V2 Advancements
- **Chat-first**: The entire flow is a conversation. No page navigation.
- **Mobile-first**: Designed for 375px-480px viewports. Works on desktop via centered card.
- **Limited clicks**: Max 3 actions per step. Most steps need only 1 tap.
- **No black box**: Every verification shows a live checklist. Errors explain what happened AND what to do next + what comes after.
- **Channel-agnostic**: The chat UI maps directly to SMS, WhatsApp, or embedded web chat.

---

## User Journey

### Phase 1: Welcome
- Bot greets user, asks for name
- User taps quick-reply or types name
- Sets personal tone for session

### Phase 2: Overview
- Shows 3-step card: Copy email → Add user → Verify
- Declares platforms (DoorDash, Uber Eats, Grubhub) and time (~12 min total)
- Quick replies: "Let's start", "How long?", "Can I do later?"
- Handles objections conversationally

### Phase 3: Platform Connect (repeats per platform)
Each platform follows an identical 3-step micro-flow:

#### Step 1 — Copy Email
- Shows Loop service email in a tappable copy field
- Quick reply: "Copied!" or "What's this email for?"
- Explains purpose if asked (read-only access, no changes)

#### Step 2 — Add User on Platform
- Shows numbered instructions (max 3 sub-steps) in a card
- Platform-specific: portal name, navigation path, role
- Warnings shown inline (e.g., Grubhub: "Admin, NOT Basic")
- Quick replies: "Done, verify!" / "I need help" / "Skip for now"
- Help reply shows detailed step-by-step with fallback

#### Step 3 — Verify
- Live animated checklist: access → role → store coverage
- **Success**: Green card, store count confirmed, "Next platform" CTA
- **Error — wrong role**: Amber card with exact 3-step fix + "Next step: once you update the role, I'll re-verify"
- **Error — not found**: Amber card explaining likely causes + "Re-verify" option
- Skip option always available

### Phase 4: Summary & Next Steps
- Summary card: per-platform status (connected / pending)
- Explains what happens next: QC check → CS kick-off
- "Coming soon" card teases post-access modules: Agents, Marketing, Finance, Protect
- Free-chat enabled for questions (keyword-matched responses + CS escalation fallback)

### Phase 5: Celebration
- Full-screen overlay confirming completion
- CTA to return to dashboard

---

## UI Components

| Component | Purpose |
|-----------|---------|
| **Chat bubble (bot)** | Left-aligned, light gray, rounded |
| **Chat bubble (user)** | Right-aligned, navy, rounded |
| **System message** | Centered, muted, phase divider |
| **Card (highlight)** | Teal-bordered instruction card |
| **Card (success)** | Green-bordered verification result |
| **Card (warning)** | Amber-bordered error with fix steps |
| **Card (error)** | Red-bordered critical failure |
| **Copy field** | Monospace email with "Tap to Copy" |
| **Quick replies** | Pill buttons below bot messages |
| **Typing indicator** | 3-dot bounce animation |
| **Progress bar** | 5-segment bar in top bar |
| **Platform pills** | Color-coded (DD red, UE green, GH red) |
| **Verification checklist** | Animated check/spinner/fail icons |
| **Progress FAB** | Floating overlay showing per-platform status |

---

## Progress & Time Tracking

- **Top bar**: 5-segment progress (Welcome → Overview → Platform → Verify → Done)
- **Time estimate**: Updates per phase (~12 min → ~10 min → ~X min → ~2 min → Done!)
- **Platform FAB**: Shows DoorDash / Uber Eats / Grubhub with dot status (green/amber/gray)

---

## Error Handling Philosophy

Every error follows this pattern:
1. **What happened** — clear, specific (e.g., "Wrong role detected: Basic instead of Full Access")
2. **How to fix it** — numbered steps (max 3)
3. **What's next** — "Once you update the role, I'll re-verify and we'll be good to go"

No dead ends. Every error has a "Re-verify" and a "Skip for now" option.

---

## Channel Adaptability

The chat structure maps to other channels:

| Feature | Web Chat | SMS/WhatsApp | Slack/Teams |
|---------|----------|--------------|-------------|
| Quick replies | Tap buttons | Numbered options ("Reply 1") | Button blocks |
| Copy field | Tap to copy | Plain text to copy | Code block |
| Cards | Rich HTML | Formatted text | Adaptive cards |
| Progress | Visual bar | Text ("2/3 platforms done") | Emoji bar |
| Verification | Animated checklist | Status updates | Threaded updates |

---

## Post-Access Onboarding (Phase 2 — Future)

After access is verified and QC is complete, self-serve onboarding expands to:

1. **Agents** — AI store management setup
2. **Marketing** — Campaign & promo configuration
3. **Finance** — Billing & reconciliation (routed to Contract Signee/CFO via Zenskar)
4. **Protect** — Dispute & chargeback management

These modules follow the same chat-first pattern.

---

## Technical Notes

- Single HTML file, zero dependencies
- Vanilla JS with centralized state object (`S`)
- CSS custom properties for theming
- `100dvh` for proper mobile viewport
- Safe area insets for notch phones
- Clipboard API with fallback selection
- No framework — easily portable to React/Vue or embedded in iframe

---

## Onboarding Lifecycle Context

This Access onboarding is step 5 in the broader customer lifecycle:

1. Opportunity ID
2. Pricing + Locations
3. Closure commitment
4. Contract signed (Products, Pricing, Billing, Admin info piped from PandaDoc → BQ/Zenskar)
5. **Self-Serve Onboarding: Access** ← this mock
   - Access & Technology Integration → Admin
   - Business & Billing → Contract Signee/CFO
6. Closed Won
7. QC Completed
8. CS Kick-off
9. Self-Serve Onboarding: Agents, Marketing, Finance, Protect
