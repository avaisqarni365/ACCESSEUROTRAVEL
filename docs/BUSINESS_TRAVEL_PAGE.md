# Business Travel Page

## Purpose
Attract corporate clients and allow them to submit a business travel request. Emphasises reliability, multi-traveller support, and corporate account benefits.

## URL
`/business`

## Target Users
- Office managers and executive assistants booking travel for staff
- SME business owners planning team travel
- Corporate HR departments managing recurring travel

---

## Layout & Sections

### 1. Page Header
- Title: "Business Travel"
- Subtitle: "Streamlined travel management for teams and companies"
- Breadcrumb: Home → Business Travel

### 2. Benefits Section
4-column grid of corporate benefits:
- Multi-Traveller Bookings
- Dedicated Account Manager
- Invoicing & Receipts
- Priority Support

Each: icon + title + 1-line description.

### 3. How It Works
3-step process:
1. Submit your company travel requirements
2. Your dedicated agent prepares a tailored quote
3. Approve and travel — invoices handled for you

### 4. Business Travel Request Form
| Field | Type | Required |
|-------|------|----------|
| Company Name | Text | Yes |
| Contact Name | Text | Yes |
| Contact Email | Email | Yes |
| Contact Phone | Tel | Yes |
| Number of Travellers | Number | Yes |
| Destination | Text | Yes |
| Travel Start Date | Date picker | No |
| Travel End Date | Date picker | No |
| Travel Requirements | Textarea (flights, hotels, transfers, visa) | No |
| Submit button | — | — |

Label: "Request Business Travel Quote"

### 5. Trusted By Section
- Logos or placeholder text for corporate clients (static)
- "Join companies already travelling with AccessEuroTravel"

### 6. CTA Strip
- "Set up a corporate account — get priority service"
- Button: "Contact Us" → `/contact`

---

## Components
- `PageHeader`
- `BenefitsGrid`
- `HowItWorks`
- `BusinessTravelForm`
- `TrustedBySection`
- `CtaStrip`

---

## User Flow
```
/business
  → Read benefits → Read how it works → Fill form → Submit
  → Success message shown → Agent receives email notification
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Submit form | `POST /api/business-travel-requests` | `business_travel_requests` |

### `POST /api/business-travel-requests` payload
```json
{
  "company_name": "string",
  "contact_name": "string",
  "contact_email": "string",
  "contact_phone": "string",
  "num_travelers": "number",
  "destination": "string",
  "travel_start": "YYYY-MM-DD (optional)",
  "travel_end": "YYYY-MM-DD (optional)",
  "requirements": "string (optional)"
}
```

---

## Special Rules / Notes
- Show inline success message after submit — do not redirect
- Trigger email notification to agent with full company details
- `customer_id` is null for guest submissions
- No food add-on anywhere on this page
- In Phase 4: add corporate account login + dashboard for repeat clients
