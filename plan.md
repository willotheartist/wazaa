# Wazaa — 2‑Week Build Plan & Run‑Log

## Timeline
**Week 1 (Oct 6–12, 2025)** — Foundations & Happy Path  
**Week 2 (Oct 13–19, 2025)** — Quality, Admin, Polish, Go‑Live

## Day-by-Day
### Week 1
1) **Mon 6 Oct** — Scaffold + Auth
- Next.js TS, Tailwind, shadcn/ui, TanStack Query
- Supabase Auth (magic link), roles & guards (`user|organizer|admin`)
- Protected routes: `/account`, `/organizer`, `/admin`

2) **Tue 7 Oct** — DB Schema + Seed
- Prisma schema + migrations
- Seed: 2 organizers, 8 workshops, 24 sessions

3) **Wed 8 Oct** — Discovery Page
- `/[city]` grid + filters (q, category, date, price)
- SEO: canonical + JSON-LD

4) **Thu 9 Oct** — Workshop Detail
- `/w/[slug]` with gallery, details, sticky session picker
- Availability validation

5) **Fri 10 Oct** — Checkout + Payments
- Seat lock (10m TTL), fee line item
- Payments test mode (Mangopay/Stripe)
- Confirmation page + ICS email

6) **Sat 11 Oct** — Organizer Dashboard v1
- Workshops CRUD, media upload, sessions calendar CRUD
- Orders (read-only), KYC flag surface

7) **Sun 12 Oct** — Buffer + A11y + Perf
- Bugfixes, LCP budget <2.0s, basic analytics

### Week 2
8) **Mon 13 Oct** — Reviews, Wishlist, Waitlist
- Post-attendance reviews; wishlist; session waitlist

9) **Tue 14 Oct** — Admin Panel
- Verify organizers, feature placements, refunds; metrics tiles

10) **Wed 15 Oct** — Map View + Emails
- Mapbox toggle; email templates; reminder ICS

11) **Thu 16 Oct** — Edge Cases
- Refund cutoffs, expired holds cleanup, partial refunds, fraud guards

12) **Fri 17 Oct** — SEO Scaling
- City/category ISR pages, sitemap, robots.txt, 404s

13) **Sat 18 Oct** — QA & Monitoring
- Happy/sad path scripts; Lighthouse ≥90; Sentry wiring; logs table

14) **Sun 19 Oct** — Launch
- Switch live payment keys; final smoke test

---

## Milestones & Exit Criteria
- **M1 (Day 5):** Book and pay end-to-end in test mode.
- **M2 (Day 7):** Organizer can publish workshops & sessions; users can book.
- **M3 (Day 10):** Admin ops + map + emails ready.
- **M4 (Day 14):** Go-live with payments.

---

## Run‑Log Template (daily)
```
# Wazaa — Build Run‑Log
## Date: YYYY-MM-DD (Day N)

### Goals
- …

### Commits / Commands
- …

### DB/Infra
- Migrations: …
- Seed: …

### UI/Pages
- …

### Decisions
- …

### Issues & Fixes
- …

### Metrics/Checks
- Lighthouse: …
- A11y: …

### Next
- …
```
