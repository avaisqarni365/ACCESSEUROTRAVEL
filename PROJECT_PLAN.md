# AccessEuroTravel — Project Plan

## Overview
AccessEuroTravel is a travel services website focused on European destinations. It provides end-to-end assistance for individuals and businesses: from visa processing to flights, hotels, and full travel packages.

---

## Services

### 1. Visa Assistance
- Country-specific visa guidance (Schengen, national visas)
- Document checklist generation
- Application status tracking
- Appointment scheduling support

### 2. Flight Booking Requests
- One-way, return, and multi-city options
- Preferred airline / cabin class selection
- Flexible date search
- Request form → agent follow-up flow

### 3. Hotel Booking Requests
Booking flow (strict order):
1. **Country** selection
2. **City** selection (filtered by country)
3. **Hotel details** — dates, room type, number of guests
4. **Food add-on** (optional) — breakfast, half-board, full-board

Food is not a standalone service; it is an optional add-on within hotel booking only.

### 4. Travel Packages
- Pre-built European itineraries (city breaks, multi-country tours)
- Custom package builder (flights + hotel + transfers)
- Group travel options
- Seasonal deals and promotions

### 5. Business Travel
- Corporate account management
- Multi-traveller bookings
- Invoice and receipt management
- Priority support line

### 6. Customer Inquiries
- General contact form
- Live chat / WhatsApp integration
- FAQ section
- Ticket / case tracking

---

## Site Structure

```
/                        Home
/visa                    Visa Assistance
/flights                 Flight Booking Requests
/hotels                  Hotel Booking Requests
  /hotels/country        Step 1 — Country
  /hotels/city           Step 2 — City
  /hotels/details        Step 3 — Hotel Details + Food add-on
/packages                Travel Packages
/business                Business Travel
/contact                 Customer Inquiries
/about                   About Us
/faq                     FAQ
```

---

## Pages & Key Components

| Page | Key Components |
|------|---------------|
| Home | Hero banner, service cards, featured packages, testimonials, CTA |
| Visa | Country selector, document checklist, inquiry form |
| Flights | Search form (origin, destination, dates, class), request submission |
| Hotels | 3-step booking wizard, food add-on toggle, summary card |
| Packages | Package grid, filter (destination, duration, budget), detail modal |
| Business | Corporate form, benefits section, account login |
| Contact | Inquiry form, live chat widget, FAQ accordion |

---

## Tech Stack (Proposed)

| Layer | Choice |
|-------|--------|
| Frontend | Next.js (React) + Tailwind CSS |
| Backend / API | Next.js API routes or Node.js (Express) |
| Database | PostgreSQL (bookings, inquiries, users) |
| Auth | NextAuth.js (customer + admin accounts) |
| Email | Resend or SendGrid (confirmations, follow-ups) |
| CMS | Sanity or Contentful (packages, FAQs, blog) |
| Hosting | Vercel (frontend) + Railway or Supabase (DB) |
| Payments | Stripe (deposit / package payment) |

---

## User Roles

| Role | Access |
|------|--------|
| Guest | Browse, submit inquiries, start booking requests |
| Customer | Full booking history, saved preferences, status tracking |
| Agent | Manage bookings, respond to inquiries, update statuses |
| Admin | Full system access, analytics, user management |

---

## Booking Request Flow

```
User fills form → Confirmation email sent → Agent reviews →
Agent contacts user (email / WhatsApp) → Quote provided →
User approves → Payment / deposit → Booking confirmed → 
Follow-up & travel docs sent
```

---

## MVP Scope

Phase 1 — MVP:
- [ ] Home page
- [ ] Visa Assistance page + inquiry form
- [ ] Flight Booking request form
- [ ] Hotel Booking 3-step wizard (country → city → details + food add-on)
- [ ] Contact / Customer Inquiries page
- [ ] Admin dashboard (view & manage requests)
- [ ] Email notifications (customer + agent)

Phase 2:
- [ ] Travel Packages with CMS
- [ ] Business Travel portal + corporate accounts
- [ ] Customer login + booking history
- [ ] Live chat / WhatsApp integration
- [ ] Payment / deposit via Stripe

Phase 3:
- [ ] Package builder (custom itineraries)
- [ ] Loyalty / repeat customer discounts
- [ ] Blog / travel guides
- [ ] Analytics dashboard

---

## Design Guidelines

- **Tone:** Professional, trustworthy, approachable
- **Color palette:** European blues and whites with warm accent (gold / amber)
- **Typography:** Clean sans-serif (e.g. Inter or DM Sans)
- **Mobile-first:** All booking flows fully responsive
- **Accessibility:** WCAG 2.1 AA compliant

---

## Key Constraints & Rules

- Food is an **optional add-on** inside hotel booking only — not a separate service
- Hotel booking always follows the order: Country → City → Hotel Details → Food (optional)
- All booking requests go through an agent review step before confirmation
- No live inventory system in MVP — request-based model only
