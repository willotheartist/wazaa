# Wazaa — UI Style Guide (v1)

## Brand Personality
Approachable, modern, practical. Big imagery, calm motion, friendly microcopy.

## Typography
- **Inter Tight** (fallback Inter)
  - H1: 42/48, semibold
  - H2: 32/38, semibold
  - H3: 24/30, medium
  - Body: 16/24, regular
  - Caption: 13/18, regular
- **Numbers**: tabular-nums for prices & counts.

## Color
- Background: `#F7F7F7`
- Surface: `#FFFFFF`
- Text Primary: `#0B0B0C`
- Text Secondary: `#5C5F66`
- Accent (Brand): `#3A61FF`
- Success: `#1FBF75`
- Warning: `#F5A524`
- Error: `#E5484D`
- Dividers/Lines: `#E7E8EA`

## Spacing & Radius
- 4-pt spacing scale (4, 8, 12, 16, 24, 32)
- Card radius **16px**; buttons **12px**; pills **28px**

## Components
- **Buttons**: Solid / Outline / Ghost; loading spinners; min height 44px.
- **Chips**: category pills; active = accent; inactive = surface + border.
- **Cards**: 16:9 image; title; meta row (★ rating, duration, price); CTA “View”.
- **Forms**: Zod validation, inline errors, helper text; drag-drop image uploader.
- **Navbar**: city switcher, search, categories; sticky on scroll.
- **Filter Bar**: sticky; chips + date picker + price slider; clear-all.
- **Session Picker**: sticky panel on detail page with upcoming sessions & seats left.
- **Tables**: compact rows; zebra on hover; sortable columns (dashboard).
- **Modals/Sheets**: create/edit session; confirm destructive actions.
- **Toasts**: success/error top-right, auto-dismiss 3–5s.
- **Empty States**: friendly icon + single primary action.
- **Tickets**: QR code, session details, refund policy link.

## Layout
- Max width 1200px; 12-col grid; generous gutters (24–32).
- Discovery: list + map toggle; infinite/paged scroll.
- Detail: media gallery top; content left; sticky picker right.
- Dashboard: 2-col layout; sidebar nav + content.

## Motion
- Durations 150–220ms; easing `ease-out`.
- Hover: image scale 1.02; elevation increase.
- Chips: fade/slide 8px on filter apply.
- Avoid parallax; prioritize snappy response.

## Accessibility
- Contrast ≥ 4.5:1
- Focus rings: `outline-2` with accent; keyboard trap-free dialogs.
- Reduced motion honored via `prefers-reduced-motion`.
- Alt text required; labels for controls; aria-live toasts.

## Content & Microcopy
- Friendly, concise: “Pick a date”, “Seats left”, “Free cancellation until …”
- Error states that teach: “Card declined — try another card or contact your bank.”

## Assets
- **Icons**: lucide-react
- **Images**: real workshop photos preferred, min 3 per listing
- **OG**: per-workshop OG with title & city

## Code Conventions
- Next.js App Router; server components for data-heavy pages.
- Tailwind tokens; shadcn/ui primitives; no inline comments in production code.
- Zod schemas for forms; TanStack Query for client data.
