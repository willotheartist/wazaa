# Wazaa — Workshops Marketplace (Web-first MVP)
**Date:** 2025-10-06 • **Scope:** Web MVP in 2 weeks (desktop-first). Mobile next quarter via Flutter/FlutterFlow wrapper or PWA.

## 1) Product Overview
- **Elevator**: “Treatwell for workshops.” Organizers publish workshops and sessions; users discover, book, and review.
- **Audiences**
  - **Users**: search, filter, book, pay, review, manage tickets & receipts.
  - **Organizers**: create listings, schedule sessions, manage capacity, view orders & payouts.
  - **Admin**: verify organizers/KYC, feature listings, handle refunds, view GMV & metrics.
- **Geo v1**: Start with 1–2 cities (e.g., Manchester, Barcelona). City switcher in nav.
- **Monetisation**: Commission per booking (launch at **5%**); paid boosts later.
- **Payments**: Mangopay (preferred) or Stripe Connect. Organizer KYC required before payouts.

## 2) Success Criteria (MVP)
- User can discover → select session → pay → receive confirmation + ICS; organizer sees order.
- No seat oversell under concurrent checkout (atomic seat lock with TTL & expiry cleanup).
- Refunds within cutoff restore seats & update payout math.
- Lighthouse ≥ 90 (Perf/Best/SEO/Accessibility) on core pages.
- Error monitoring active (Sentry) with no PII in logs.

## 3) System Architecture (Web)
- **Frontend**: Next.js 15 (App Router) + TypeScript + Tailwind, shadcn/ui, TanStack Query. Mapbox for map view.
- **Backend**: Next.js Route Handlers (API) + Prisma to Postgres (Supabase). Zod validation.
- **Auth**: Supabase Auth (magic link). JWT claim `role: user|organizer|admin`.
- **Payments**: Mangopay/Stripe Connect; platform fee line item.
- **Storage**: Supabase Storage/S3 for media; Sharp for server transforms.
- **Email**: Resend/SendGrid; transactional templates; ICS attachments.
- **Observability**: Sentry; audit logs table.
- **SEO**: SSR for discovery & detail; JSON-LD Event schema; ISR for city/category pages.

## 4) Role-Based UX & Navigation
- **Anonymous**: Explore, Categories, Sign in.
- **User**: Explore, My Bookings, Wishlist, Account.
- **Organizer**: Dashboard, Workshops, Sessions, Orders, Payouts, Settings.
- **Admin**: Overview, Organizers, Refunds, Features, System.

### Dual Signup/Login
- Separate entry points: `/signin` decides route after login by role.
- **Organizer onboarding**: company/legal name, location, payout setup (KYC), brand assets.
- **User onboarding**: name, city, interests (categories).

## 5) IA & Screens
### Public
- **Home/Explore `/[city]`**: search bar, category chips, date/price filters, list + map toggle.
- **Workshop detail `/w/[slug]`**: gallery, details, what’s included, host profile, FAQs, **sticky “Pick a date”** with upcoming sessions & seats left.
- **Checkout `/checkout/:bookingId`**: attendees qty, summary (fee lines), payment, terms.
- **Success/Cancel**: order summary, next steps, download ICS, open ticket.
- **Static**: About, Help/Refund policy, Terms/Privacy, Sitemap.

### User Account `/account`
- **Bookings**: upcoming (QR tickets), past (review prompt).
- **Wishlist**: saved workshops.
- **Payments**: invoices/receipts.
- **Profile**: basics, notifications, delete account.

### Organizer Dashboard `/organizer`
- **Home**: KPIs (today’s attendees, week revenue, low seats).
- **Workshops**: CRUD (title, slug, category, description MD, duration, skills, images).
- **Sessions**: calendar (month/week) + CRUD (start/end, price, capacity, cutoff).
- **Orders**: bookings table; status; refund/mark-attended actions.
- **Promos**: codes, limits, expiry.
- **Payouts**: KYC status, upcoming payouts, history.
- **Settings**: profile, team seats, locations, policies.

### Admin `/admin`
- **Overview**: GMV, take rate, fill rate, refund rate.
- **Organizers**: verify/KYC, feature flags.
- **Refunds**: manual approvals.
- **Features**: featured placements by city/category.
- **System**: jobs/queues, email logs.

## 6) Data Model (Prisma shape)
```
model User        {{ id String @id @default(cuid()); email String @unique; name String?; role Role @default(user); createdAt DateTime @default(now()) }}
enum Role         {{ user organizer admin }}

model Organizer   {{ id String @id @default(cuid()); userId String @unique; user User @relation(fields: [userId], references: [id]); displayName String; bio String?; kycStatus Kyc @default(pending); payoutAccountId String?; createdAt DateTime @default(now()) }}
enum Kyc          {{ pending verified rejected }}

model Workshop    {{ id String @id @default(cuid()); organizerId String; organizer Organizer @relation(fields: [organizerId], references: [id]); title String; slug String @unique; category String; descriptionMd String; durationMin Int; minAge Int?; coverUrl String; gallery String[]; address String; lat Float; lng Float; status Status @default(draft); createdAt DateTime @default(now()) }}
enum Status       {{ draft live archived }}

model Session     {{ id String @id @default(cuid()); workshopId String; workshop Workshop @relation(fields: [workshopId], references: [id]); startAt DateTime; endAt DateTime; pricePennies Int; capacity Int; seatsLeft Int; cancelCutoffHours Int @default(24); createdAt DateTime @default(now()) }}

model Booking     {{ id String @id @default(cuid()); userId String; user User @relation(fields: [userId], references: [id]); sessionId String; session Session @relation(fields: [sessionId], references: [id]); qty Int; status BookingStatus @default(pending); totalPennies Int; feePennies Int; holdExpiresAt DateTime?; createdAt DateTime @default(now()); tickets Ticket[] }}
enum BookingStatus {{ pending paid canceled refunded expired }}

model Ticket      {{ id String @id @default(cuid()); bookingId String; booking Booking @relation(fields: [bookingId], references: [id]); qrCode String; status TicketStatus @default(valid) }}
enum TicketStatus {{ valid used refunded }}

model Review      {{ id String @id @default(cuid()); userId String; workshopId String; rating Int; text String?; createdAt DateTime @default(now()) }}

model PromoCode   {{ id String @id @default(cuid()); organizerId String?; code String @unique; pctOff Int; maxUses Int?; used Int @default(0); expiresAt DateTime? }}

model PayoutEvent {{ id String @id @default(cuid()); organizerId String; amountPennies Int; status String; externalRef String?; createdAt DateTime @default(now()) }}

model AuditLog    {{ id String @id @default(cuid()); actorId String?; action String; entity String; entityId String; meta Json?; createdAt DateTime @default(now()) }}
```

## 7) Booking & Seat Locking
- On “Go to checkout” → create `Booking(pending)` with `qty`, decrement `Session.seatsLeft` atomically, set `holdExpiresAt = now()+10m`.
- A scheduled job/cron releases expired holds: set booking → `expired`, increment `seatsLeft` by `qty`.
- Payment success → `paid`, generate `Ticket` rows, email confirmation with ICS; failure → restore seats.

## 8) Payments & Refunds
- Platform fee shown as line item; capture full amount; schedule split payout to organizer minus fees after cutoff or after session completion.
- Refund rules: before `cutoffHours` → full (minus processing if needed); within cutoff → partial or organizer-defined; after session start → no refund (admin override).

## 9) Emails & ICS
- **Confirmation**: items, dates, location, QR ticket(s), “Add to calendar” ICS.
- **Reminder (24h)**: bring requirements, location map link, policy recap.
- **Organizer alert**: new booking + attendee count.
- **Refund**: amount & timeline.

## 10) SEO & Content
- City/category pages with ISR, human-readable slugs, JSON-LD (Event). OpenGraph images per workshop. Alt text required.
- Blog/Guides later (not MVP).

## 11) Security & Compliance
- KYC for organizers (Mangopay/Stripe requirements).
- GDPR: DSR endpoints, retention policy, cookie banner, privacy policy.
- Rate limiting on auth & booking intents.

## 12) Launch Checklist
- Payments live keys; webhooks verified.
- Concurrency tests (simulate 20 parallel checkouts).
- 404/empty states; sane fallbacks.
- Monitoring dashboards (errors, GMV, take rate, fill rate, conversion).
