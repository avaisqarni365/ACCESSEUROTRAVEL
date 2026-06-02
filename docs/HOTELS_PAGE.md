# Hotels Page

## Purpose
Allow users to submit a hotel booking request using a strict 3-step wizard. Food is an optional add-on at the end of the wizard — it is not a separate service.

## URL
`/hotels`

## Target Users
- Individuals and families booking accommodation in Europe
- Package travellers who need hotel-only add-ons
- Business travellers needing hotel stays per city

---

## Layout & Sections

### 1. Page Header
- Title: "Hotel Booking"
- Subtitle: "Find the perfect stay across Europe"
- Breadcrumb: Home → Hotel Booking

### 2. Booking Wizard

The wizard is a single page with 3 steps. The URL does not change between steps — state is held in component.

---

#### Step 1 — Country
- Step indicator: "Step 1 of 3 — Select Country"
- Dropdown: all active countries from `countries` table
- Next button (disabled until a country is selected)

#### Step 2 — City
- Step indicator: "Step 2 of 3 — Select City"
- Dropdown: cities filtered by the country selected in Step 1 (from `cities` table)
- Back button + Next button (Next disabled until city is selected)

#### Step 3 — Hotel Details
- Step indicator: "Step 3 of 3 — Hotel Details"
- Fields:

| Field | Type | Required |
|-------|------|----------|
| Hotel Name | Text | No |
| Check-In Date | Date picker | Yes |
| Check-Out Date | Date picker | Yes |
| Room Type | Select: Single / Double / Twin / Suite / Family | Yes |
| Number of Guests | Number (1–20) | Yes |
| Food Add-On | Radio: None / Breakfast / Half-Board / Full-Board | Yes (default: None) |
| Special Requests | Textarea | No |
| First Name | Text | Yes |
| Last Name | Text | Yes |
| Email | Email | Yes |
| Phone | Tel | Yes |

- Back button + **Submit Request** button

---

### 3. Booking Summary Card
- Shown above the submit button in Step 3
- Displays: Country, City, Dates, Room Type, Guests, Food Add-On

### 4. Success State
- Shown after successful submission (replaces the wizard)
- Confirmation message + "Submit Another Request" button

---

## Components
- `PageHeader`
- `WizardStepIndicator`
- `Step1Country` (country dropdown)
- `Step2City` (city dropdown, filtered)
- `Step3HotelDetails` (full form + food add-on)
- `BookingSummaryCard`
- `SuccessMessage`

---

## User Flow
```
/hotels
  → Step 1: Select country → Next
  → Step 2: Select city (filtered by country) → Next
  → Step 3: Fill hotel details + food add-on (optional) → Review summary → Submit
  → Success message shown
  → Agent receives email notification
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Load countries | `GET /api/countries` | `countries` |
| Load cities by country | `GET /api/cities?country_id=X` | `cities` |
| Submit request | `POST /api/hotel-requests` | `hotel_requests` |

### `POST /api/hotel-requests` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "country_id": "number",
  "city_id": "number",
  "hotel_name": "string (optional)",
  "check_in": "YYYY-MM-DD",
  "check_out": "YYYY-MM-DD",
  "room_type": "single|double|twin|suite|family",
  "num_guests": "number",
  "food_addon": "none|breakfast|half_board|full_board",
  "special_requests": "string (optional)"
}
```

---

## Special Rules / Notes
- **Strict wizard order: Country → City → Hotel Details. This order must never change.**
- **Food is an optional add-on only — it is not a separate service and must not appear anywhere else on the site.**
- City dropdown must reset when country changes
- Check-out date must be after check-in date
- Food add-on defaults to "None" — user must actively choose it
- Show success message inline — do not redirect
- Trigger email notification to agent on submit
- Countries and cities must only show `is_active = 1` records
