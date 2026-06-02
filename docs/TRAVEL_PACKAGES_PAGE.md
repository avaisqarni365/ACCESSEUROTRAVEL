# Travel Packages Page

## Purpose
Showcase pre-built European travel packages and allow users to request a custom itinerary. Content is CMS-driven (Phase 2). Phase 1 uses static placeholder data.

## URL
`/packages`

## Target Users
- Leisure travellers wanting a ready-made itinerary
- First-time visitors to Europe unsure where to start
- Groups and families wanting a managed trip experience

---

## Layout & Sections

### 1. Page Header
- Title: "Travel Packages"
- Subtitle: "Handpicked European itineraries — or build your own"
- Breadcrumb: Home → Travel Packages

### 2. Filter Bar
Horizontal filter row above the package grid:
- Destination (dropdown: country)
- Duration (select: Any / 3–5 days / 6–9 days / 10+ days)
- Budget (select: Any / Under €500 / €500–€1000 / €1000+)
- Reset Filters button

### 3. Packages Grid
- 3-column grid (2 on tablet, 1 on mobile)
- Each `PackageCard` contains:
  - Hero image
  - Destination badge (country flag + name)
  - Package title
  - Duration (e.g. "7 Days / 6 Nights")
  - Inclusions icons: ✈ Flights | 🏨 Hotel | 🚌 Transfers
  - Price "From €XXX per person"
  - CTA button: "View Details"
- Empty state message if no packages match filters

### 4. Package Detail Modal / Page
Opened when "View Details" is clicked:
- Full description
- Day-by-day itinerary
- Inclusions list
- Price breakdown
- CTA button: "Request This Package" → opens request form

### 5. Package Request Form
Triggered from detail view CTA:
- Package name (pre-filled, read-only)
- Destination country + city (pre-filled, read-only)
- Start date (date picker)
- End date (date picker)
- Number of travellers
- Budget (optional)
- Include flights? (toggle, default on)
- Include hotel? (toggle, default on)
- Include transfers? (toggle, default off)
- Notes / special requests
- First Name, Last Name, Email, Phone
- Submit: "Request Package"

### 6. Custom Package Builder
Separate section below the grid:
- Headline: "Can't find what you're looking for? Build your own."
- Same form as Package Request Form but without pre-filled package name
- Destination fields are editable
- CTA: "Request Custom Package"

---

## Components
- `PageHeader`
- `FilterBar`
- `PackageCard`
- `PackagesGrid`
- `PackageDetailModal`
- `PackageRequestForm`
- `CustomPackageBuilder`
- `EmptyState`

---

## User Flow
```
/packages
  → Browse grid → Apply filters → Click "View Details"
  → Read detail → Click "Request This Package"
  → Fill form → Submit → Success message
  → Agent receives email notification

  OR

  → Scroll to Custom Builder → Fill form → Submit → Success message
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Load packages (Phase 1) | Static data | — |
| Load packages (Phase 2) | Sanity CMS API | — |
| Load countries | `GET /api/countries` | `countries` |
| Load cities by country | `GET /api/cities?country_id=X` | `cities` |
| Submit package request | `POST /api/package-requests` | `package_requests` |

### `POST /api/package-requests` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "package_name": "string (optional)",
  "destination_country_id": "number (optional)",
  "destination_city_id": "number (optional)",
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "num_travelers": "number",
  "budget": "number (optional)",
  "includes_flights": "boolean",
  "includes_hotels": "boolean",
  "includes_transfers": "boolean",
  "notes": "string (optional)"
}
```

---

## Special Rules / Notes
- Phase 1: use static placeholder package data (3–6 cards)
- Phase 2: replace with Sanity CMS fetch (no breaking changes to the grid component)
- Filters are client-side in Phase 1; server-side in Phase 2
- "Food" is never shown as a package inclusion option — it is a hotel add-on only
- Show inline success message after form submit — do not redirect
- Trigger email notification to agent on submit
