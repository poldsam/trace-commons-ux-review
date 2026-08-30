# Contributor App UX Review — "Connect Once and Forget"

Date: 2026-08-30
Scope: macOS app (`macos/Sources/`) and GTK Linux app (`crates/trace-commons-contributor-gtk/`)
Lens: Can a user connect once, forget about it, and trust the reward arrives — with the
app doing the deciding for them?

## Verdict

The app is currently built for the *opposite* user. Its entire design center is
contributor agency — "nothing is sent unless you say so" — expressed as a per-trace
review gate, six onboarding decisions, and eight-plus configuration knobs. That is a
coherent product, but it is a **high-agency, high-attention** product. The user you
describe — connect once, delegate the judgment, trust the payout — is not served by the
default path and on macOS cannot reach it at all.

The gap is not polish. It is a philosophical default that has to be inverted.

## What stands between "connect" and "forget" today

### 1. Onboarding demands 6 decisions before the first contribution

Both platforms gate contribution behind 6 sequential screens: Welcome → Connect → Consent
scopes → Privacy scan → Roots/Watch → Done. Only two are genuinely load-bearing for a
trusting user (Connect the invite, and grant consent). The rest — which folders to watch,
which projects to include, whether to add a NEAR AI scan — are exactly the decisions a
"just handle it" user wants delegated. Nothing is pre-selected by design: the macOS roots
screen "opens undecided and needs one deliberate action" per row.

**Cost:** the very first experience teaches the user that this app is a series of choices,
not a switch.

### 2. Every trace is a decision — and there is no true "forget" default

This is the core issue.

- **macOS:** there is **no auto-approve at all**. Every trace must be individually
  Submitted, previewed-then-Contributed, or dismissed ("Not this one"). The `auto_upload`
  mode exists in the daemon but is *deliberately not exposed* in the UI. Bulk "Submit all"
  exists but the code comments frame one-click as "availability, not a recommendation to
  skip looking." A macOS user literally cannot set-and-forget.
- **GTK:** there *is* a per-project "Contribute automatically" mode — but it is opt-in per
  project, buried in Settings, and gated behind a deliberately alarming confirmation:
  *"Every future session in this project will be scrubbed and contributed without asking
  you. You won't review them first."*

So the closest thing to "forget" is: finish onboarding, go into Settings, arm each project
one at a time, and click through a warning designed to make you reconsider. That is the
delegation path presented as a hazard.

### 3. The reward is opaque exactly where trust is formed

The user's premise is "trust they get the reward." The app actively undercuts that:

- Credit appears **only on the History page**, never in the moment of contributing. The
  submission toast says *"Approved 47. Scrubbing removed 213. 3 flagged."* — it reports
  privacy mechanics, not reward.
- There is no notification when credit settles. The user must re-open the app and navigate
  to History to discover whether anything happened.
- The headline copy tells them not to expect a reward: *"credit is a record, not a
  currency: there is no payout, no token, no exchange rate, and no date… Contribute because
  you want the commons to exist."*

That last line is honest and, for the current audience, probably right. But it is the exact
opposite of "connect once and trust the reward shows up." You cannot ask a user to trust a
reward while the flagship copy tells them there is no reward and none is promised.

### 4. Configuration surface is broad

Beyond onboarding: quiescence time, undo/hold window, digest interval, max uploads/day, max
bytes/day, pause duration, autostart, public-profile enrollment, per-project mode, consent
re-review. Each is individually defensible and well-written. Collectively they signal a
product that expects to be tuned, not trusted.

## Platform inconsistency worth flagging

macOS and GTK have *diverged on the central question*. GTK offers armed auto-contribution;
macOS forbids it in the UI. Whatever direction you choose, the two front-ends should not
disagree about whether delegation is even allowed. Right now a Linux user and a Mac user
have categorically different products.

## Recommendations

Ordered by leverage for the connect-once-and-forget user. None require abandoning the
high-agency mode — the goal is to make delegation the *default* and review the *opt-in*,
inverting today's polarity.

### A. Make "Trust it" a first-class onboarding choice (highest leverage)

Add one screen after Connect: **"How hands-on do you want to be?"** with two options:

- **Trust it (recommended):** the app watches, scrubs, and contributes automatically. You
  can review anytime, but you don't have to. → arms all discovered projects, sets sensible
  defaults, skips the roots/projects/scan decision screens.
- **Review each one:** today's behavior.

This single fork lets the target user finish onboarding in two taps (Connect, Trust it) and
collapses screens 3–6 into defaults. It also resolves the platform split by making
delegation a supported, non-scary path on *both* front-ends.

### B. Reframe auto-contribution from hazard to feature

The GTK arming dialog and the macOS refusal both treat delegation as dangerous. If the
local scrubbing is trustworthy enough to ship, it is trustworthy enough to be the default —
say so. Replace *"You won't review them first"* with what the app does *for* the user:
"We scrub every trace locally before it leaves your machine. You can spot-check anytime in
History." Keep the undo window as the safety net instead of pre-review.

### C. Close the reward loop in the moment

- Make the submission toast reward-forward: "Contributed 47 sessions — credit is being
  scored" with a tap-through to History.
- Notify (respecting digest cadence) when credit settles: "Your contributions were accepted
  into the commons."
- If credit truly cannot be promised yet, the trust story has to rest on *acceptance* and
  *impact* ("in the commons") rather than payout — surface those proactively, not only on a
  page the user has to go find.

### D. Shrink the default configuration surface

Keep every knob, but hide them behind an "Advanced" disclosure. The trusting user should
never see quiescence time, digest interval, or byte budgets unless they go looking. Defaults
should be good enough that they never need to.

### E. Unify the two front-ends on the delegation model

Pick one answer to "is auto-contribution allowed and default?" and implement it identically
on macOS and GTK. The daemon already supports `auto_upload`; this is a front-end policy
decision, not new plumbing.

## What to preserve

- The local-scrub-before-send guarantee and the "exactly what would be sent" transparency.
  These are the *reason* delegation can be safe; they should stay, just not be mandatory to
  read.
- The honest, non-gamified tone. Inverting to "trust it" does not require streaks, badges,
  or fake payout promises — it requires good defaults and a closed reward loop.
- The undo window as the safety net that makes hands-off contribution defensible.

## Bottom line

Today the app asks the user to *decide*, over and over, and tells them not to expect a
reward. The user you want to serve wants to *decide once* and *trust the reward*. The
machinery to serve them already exists (`auto_upload`, local scrubbing, the undo window,
credit records) — it is gated, hidden, or, on macOS, switched off. The work is mostly
inverting defaults and closing the reward loop, not building new capability.
