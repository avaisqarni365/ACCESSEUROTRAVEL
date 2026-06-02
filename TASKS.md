# AccessEuroTravel — Development Tasks

Track progress by checking boxes. Work phase by phase — do not start a new phase until the current one is stable.

---

## Phase 0 — Project Setup

### Environment
- [ ] Install Node.js (v20+), VS Code, Git
- [ ] Install VS Code extensions: ESLint, Prettier, Tailwind CSS IntelliSense, MySQL (by cweijan), GitLens
- [ ] Create Next.js app: `npx create-next-app@latest accesseurotravel --typescript --tailwind --eslint --app`
- [ ] Configure Prettier + ESLint in project root
- [ ] Set up `.env.local` with DB, auth, email, and Stripe keys
- [ ] Create MySQL database locally and run all `CREATE TABLE` statements from `DATABASE_DESIGN.md`
- [ ] Seed `countries` and `cities` tables
- [ ] Push initial Next.js scaffold to GitHub
- [ ] Connect repo to Vercel (auto-deploy on push to `main`)

### Folder Structure
```
/app               Next.js App Router pages
/components        Reusable UI components
/lib               DB client, auth helpers, utilities
/types             TypeScript interfaces
/public            Static assets (images, icons)
/styles            Global CSS
```

---

## Phase 1 — MVP: Core Pages & Booking Flows

### 1.1 Layout & Navigation
- [ ] Create `Header` component (logo, nav links, mobile menu)
- [ ] Create `Footer` component (links, contact info, social)
- [ ] Create root `layout.tsx` with Header + Footer
- [ ] Add favicon and brand logo

### 1.2 Home Page (`/`)
- [ ] Hero banner with headline and CTA buttons
- [ ] Services grid (6 cards: Visa, Flights, Hotels, Packages, Business, Contact)
- [ ] Featured packages section (static placeholders for now)
- [ ] Testimonials section
- [ ] Call-to-action strip ("Start your journey")

### 1.3 Visa Assistance Page (`/visa`)
- [ ] Page layout with intro and process steps
- [ ] Country selector dropdown (from `countries` table)
- [ ] Visa type selector (tourist, business, student, transit, family, work)
- [ ] Inquiry form (name, email, phone, travel date, purpose)
- [ ] Form submission → insert into `visa_requests` table
- [ ] Confirmation message shown after submission
- [ ] Email notification to agent on new submission (Resend / SendGrid)

### 1.4 Flight Booking Request Page (`/flights`)
- [ ] Trip type toggle (one-way / return / multi-city)
- [ ] Origin and destination inputs
- [ ] Date pickers (departure, return)
- [ ] Cabin class selector
- [ ] Passenger count input
- [ ] Preferred airline + special requests fields
- [ ] Form submission → insert into `flight_requests` table
- [ ] Confirmation message + email notification to agent

### 1.5 Hotel Booking Wizard (`/hotels`)
- [ ] **Step 1 — Country:** dropdown from `countries` table
- [ ] **Step 2 — City:** dropdown filtered by selected country (from `cities` table)
- [ ] **Step 3 — Hotel Details:**
  - Hotel name (optional)
  - Check-in / check-out date pickers
  - Room type selector (single, double, twin, suite, family)
  - Number of guests
  - Food add-on toggle (none / breakfast / half-board / full-board)
  - Special requests textarea
- [ ] Step progress indicator (Step 1 of 3)
- [ ] Back/Next navigation between steps
- [ ] Summary card before final submit
- [ ] Form submission → insert into `hotel_requests` table
- [ ] Confirmation message + email notification to agent

### 1.6 Customer Inquiries / Contact Page (`/contact`)
- [ ] Contact form (name, email, phone, service interest, message)
- [ ] Form submission → insert into `leads` table + `messages` table
- [ ] FAQ accordion section (static content)
- [ ] WhatsApp button (link to WhatsApp number)
- [ ] Confirmation message after submission

### 1.7 About Page (`/about`)
- [ ] Company story and mission
- [ ] Team section (static)
- [ ] Why choose us (trust signals)

### 1.8 API Routes (Next.js)
- [ ] `POST /api/visa-requests`
- [ ] `POST /api/flight-requests`
- [ ] `POST /api/hotel-requests`
- [ ] `POST /api/leads`
- [ ] Input validation on all routes (Zod)
- [ ] Rate limiting on form endpoints

---

## Phase 2 — Admin Dashboard

### 2.1 Auth
- [ ] Set up NextAuth.js with credentials provider (email + password)
- [ ] Protect `/admin/**` routes via middleware
- [ ] Login page (`/admin/login`)
- [ ] Logout button in admin header

### 2.2 Admin Layout
- [ ] Admin sidebar (Dashboard, Requests, Leads, Messages, Settings)
- [ ] Admin header with logged-in user name and role

### 2.3 Dashboard Overview (`/admin`)
- [ ] Stats cards: total requests, pending, confirmed, new leads
- [ ] Recent requests feed (last 10 across all types)
- [ ] Quick-action buttons (view pending, view new leads)

### 2.4 Request Management
- [ ] `/admin/hotel-requests` — table with filters (status, date, agent)
- [ ] `/admin/flight-requests` — table with filters
- [ ] `/admin/visa-requests` — table with filters
- [ ] `/admin/package-requests` — table with filters
- [ ] `/admin/business-requests` — table with filters
- [ ] Detail view for each request type
- [ ] Status update dropdown on each request
- [ ] Assign agent to a request
- [ ] Add agent notes
- [ ] Quoted price input field

### 2.5 Leads Management (`/admin/leads`)
- [ ] Leads table with status filter
- [ ] Mark lead as contacted / converted / lost
- [ ] Convert lead to customer (create entry in `customers` table)

### 2.6 Messages (`/admin/messages`)
- [ ] Inbox view (all incoming messages)
- [ ] Reply to a message (insert into `messages` with agent sender)
- [ ] Mark as read

### 2.7 API Routes (Admin)
- [ ] `GET /api/admin/requests` (all types, filterable)
- [ ] `PATCH /api/admin/requests/:type/:id` (status, agent, notes, price)
- [ ] `GET /api/admin/leads`
- [ ] `PATCH /api/admin/leads/:id`
- [ ] `GET /api/admin/messages`
- [ ] `POST /api/admin/messages/:id/reply`

---

## Phase 3 — Travel Packages & Business Travel Portal

### 3.1 Travel Packages (`/packages`)
- [ ] Set up Sanity CMS (or Contentful) for package content
- [ ] Package schema: title, destination, duration, price, inclusions, images, highlights
- [ ] Packages listing page with filter (destination, duration, budget)
- [ ] Package detail page
- [ ] "Request this package" button → package request form → `package_requests`
- [ ] Custom package builder form (select inclusions manually)
- [ ] Seasonal deals section on home page (CMS-driven)

### 3.2 Business Travel Portal (`/business`)
- [ ] Business landing page (benefits, corporate features)
- [ ] Corporate inquiry form → `business_travel_requests`
- [ ] Email notification to agent with full details

---

## Phase 4 — Customer Accounts & Payments

### 4.1 Customer Accounts
- [ ] Customer registration page (`/register`)
- [ ] Customer login page (`/login`)
- [ ] Customer dashboard (`/account`)
  - [ ] Booking history (all request types)
  - [ ] Request status tracker
  - [ ] Profile settings (update name, phone, passport info)
  - [ ] Document upload (attach files to a request)
- [ ] Link existing `customers` record to new `users` record on registration

### 4.2 Documents
- [ ] File upload component (drag & drop)
- [ ] `POST /api/documents` — upload file to storage (S3 or Vercel Blob), save metadata to `documents` table
- [ ] Document viewer in admin detail view
- [ ] Document list in customer account

### 4.3 Invoices
- [ ] Invoice generator in admin (select request → create invoice)
- [ ] Invoice detail page (`/admin/invoices/:id`)
- [ ] PDF export of invoice
- [ ] Email invoice to customer

### 4.4 Payments (Stripe)
- [ ] Set up Stripe account and add keys to `.env`
- [ ] `POST /api/payments/create-intent` — create Stripe payment intent from invoice
- [ ] Payment page (`/pay/:invoiceId`) with Stripe Elements
- [ ] Webhook handler (`/api/webhooks/stripe`) — update `payments` and `invoices` on success
- [ ] Payment confirmation email to customer

---

## Phase 5 — Live Chat, Polish & Launch

### 5.1 Live Chat / WhatsApp
- [ ] Integrate WhatsApp Business link (floating button on all pages)
- [ ] Optional: integrate Tawk.to or Crisp live chat widget

### 5.2 SEO & Performance
- [ ] Add `metadata` to all pages (title, description, OG tags)
- [ ] Add `sitemap.xml` and `robots.txt`
- [ ] Optimise all images with `next/image`
- [ ] Lighthouse audit — target 90+ score on all pages

### 5.3 Accessibility
- [ ] WCAG 2.1 AA audit (keyboard nav, ARIA labels, colour contrast)
- [ ] Test with screen reader

### 5.4 Testing
- [ ] Unit tests for all API route handlers (Jest)
- [ ] Integration tests for booking form flows (Playwright)
- [ ] Test all email notifications end-to-end
- [ ] Test Stripe webhook with Stripe CLI (`stripe listen`)

### 5.5 Launch Checklist
- [ ] Set all production environment variables in Vercel
- [ ] Point custom domain to Vercel
- [ ] Enable HTTPS (automatic via Vercel)
- [ ] Set up DB backups (daily)
- [ ] Set up error monitoring (Sentry)
- [ ] Final cross-browser test (Chrome, Firefox, Safari, mobile)
- [ ] Go live

---

## Backlog (Phase 6+)

- [ ] Blog / travel guides (CMS-driven)
- [ ] Loyalty discounts for repeat customers
- [ ] Multi-language support (EN / FR / AR)
- [ ] Analytics dashboard for admin (bookings by month, revenue, top destinations)
- [ ] Automated email sequences (pre-travel reminders, post-trip follow-up)
- [ ] Customer reviews and ratings

---

## Notes

- All booking forms must validate input with **Zod** before DB insert
- Hotel booking always follows the strict order: Country → City → Details → Food add-on
- Food is an optional add-on in hotel booking only — not a separate service anywhere
- Never expose raw DB errors to the client — always return generic error messages
- Use `DECIMAL(10,2)` for all prices; never use floats for money
