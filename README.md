# AESTHETICNOVA

A production-shaped e-commerce application for an Indian stainless-steel streetwear
jewellery brand — built with a real server, real inventory, real consent mechanics and an
honest launch-readiness report.

**Stack:** Node 20 · Express 4 · EJS (server-rendered) · SQLite (better-sqlite3) · no client framework, no CDN, no third-party script.

```
npm install
npm run seed        # catalogue, tracker register, settings
npm start           # http://localhost:3000
```

---

## What actually works

| Area | Status |
|---|---|
| Catalogue, search, filter, sort, stock | Real — 15 products, 33 variants, live stock counts |
| Product page: zoom, variants, honest review states | Real |
| Bag, checkout, GST maths, itemised totals | Real — one pricing engine drives every screen |
| Order records, cancellation, tracking lookup | Real |
| Accounts, wishlist, verified-purchase reviews | Real |
| Consent system + auditable receipts | Real — server-enforced, not cosmetic |
| Privacy & Data Requests (DSAR) workflow | Real — reference, verification, admin execution |
| Support and grievance tickets | Real — acknowledgement, history, status page |
| Admin console (orders, stock, reviews, consent log, retention, audit) | Real |
| Retention/erasure job | Real — 9 rules, dry-run + apply |
| **Payments** | **Simulator only.** No merchant account is connected; no money moves; the UI says so on every payment screen |
| **Email / SMS** | **Not configured.** Messages are written to an `outbox` table for manual sending; the UI says so |
| **Courier / tracking** | **Not integrated.** No tracking numbers are shown because none exist |
| **Product photography** | Illustrative renders generated for this build, labelled as such on product pages and in the licence register |

## Privacy architecture in one paragraph

Three first-party cookies (`an_sid`, `an_consent`, and nothing else). A Content-Security-Policy
with **no external origins**. Fonts self-hosted. No analytics SDK, no pixel, no tag manager,
no chat widget, no embed. The only optional processing is an anonymous, cookieless,
server-side visit counter that does not run unless analytics consent is on — and that is
verified by measuring database rows before and after browsing, not by reading the UI.
Every accept, reject and withdrawal mints an immutable receipt with a one-way hash of the
IP address rather than the address itself.

## Verification

```
npm run audit          # contrast + accessibility + end-to-end, writes data/test-results.json
npm run test:contrast  # 48 design-system colour pairings against WCAG thresholds
npm run test:a11y      # real Chromium: keyboard, focus, landmarks, alt, rendered contrast, reflow
npm run test:e2e       # consent gating, fees, reviews, DSAR, security, licences, claims
```

Current result: **104 automated checks, 104 pass.** The live report at `/launch-readiness`
reads that file, so a stale or failing run is never presented as success.

## Where the honesty lives

- `/launch-readiness` — every one of the 18 requirements marked **PASS / FAIL / NOT CONFIGURED / NEEDS HUMAN REVIEW**, derived from live config + database + test results. Machine-readable at `/launch-readiness.json`.
- `/transparency` — tracker inventory, ranking disclosure, dark-pattern self-audit history.
- `/business-details` — renders "not configured" instead of inventing an entity, address or GSTIN.
- `/about` — a claim-by-claim table of what we refuse to say (hypoallergenic, waterproof, nickel-free, 316L, lifetime guarantee) and why.

## Configuration

Nothing business-specific is hard-coded. Supply these environment variables; anything
missing renders as "not configured" in the UI and as **NOT CONFIGURED** in the report.

```bash
NODE_ENV=production
PORT=3000
FORCE_HTTPS=1  HSTS=1
ADMIN_EMAIL=you@yourdomain.in
ADMIN_PASSWORD=            # REQUIRED — leaving this unset is a launch blocker
PRIVACY_HASH_SALT=         # REQUIRED in production — random 32+ chars

PAYMENT_MODE=demo          # or 'razorpay' with live keys below
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
RAZORPAY_WEBHOOK_SECRET=
COD_ENABLED=0  COD_FEE_PAISE=4900

EMAIL_MODE=none            # 'smtp' | 'provider' — none writes to the outbox
SMS_MODE=none

BIZ_TRADING_NAME=AESTHETICNOVA
BIZ_LEGAL_ENTITY=
BIZ_ADDRESS=
BIZ_SUPPORT_EMAIL=
BIZ_SUPPORT_PHONE=
BIZ_GSTIN=
BIZ_GRIEVANCE_OFFICER=
BIZ_GRIEVANCE_EMAIL=
BIZ_SUPPORT_HOURS=
BIZ_COUNTRY_OF_ORIGIN=India
BIZ_SELLER_NAME=
```

## Documentation

| File | Contents |
|---|---|
| `docs/SECURITY-OPERATIONS.md` | Transport, sessions, secrets, rate limits, retention schedule, incident runbook, deployment checklist |
| `docs/LEGAL-REVIEW-PACK.md` | Facts asserted with sources, 17 questions for counsel, sign-off block |
| `docs/DARK-PATTERN-SELF-AUDIT.md` | The completed annual self-audit with evidence per check |
| `docs/DATA-PROCESSING-REGISTER.md` | Processing activities and the verified third-party inventory |

## Layout

```
src/        config, db schema, security, session, consent, pricing, payments, retention, readiness
routes/     pages, shop, cart, checkout, auth, reviews, support, privacy-requests, consent, admin
views/      EJS templates — partials, shop, cart, checkout, account, admin, legal, info
public/     css/app.css design system, js (no dependencies), self-hosted fonts, images
tests/      contrast, a11y (Playwright), e2e (Playwright + HTTP + SQL), run-all
docs/       operations, legal review, self-audit, processing register
```

## Not legal advice

Nothing in this repository establishes legal compliance. The policy pages are drafted
carefully and marked for review. See `docs/LEGAL-REVIEW-PACK.md`.
