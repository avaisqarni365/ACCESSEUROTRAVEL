# Flights Page

## Purpose
Allow users to submit a flight booking request. This is a request-based flow — not live inventory. An agent reviews and follows up with a quote.

## URL
`/flights`

## Target Users
- Individuals booking one-way or return flights to Europe
- Travellers with complex multi-city itineraries
- Business travellers needing specific cabin classes

---

## Layout & Sections

### 1. Page Header
- Title: "Flight Booking"
- Subtitle: "Tell us where you want to go and we'll find the best options for you"
- Breadcrumb: Home → Flight Booking

### 2. How It Works
3-step summary:
1. Fill in your flight details
2. Our team finds the best options
3. We contact you with a quote

### 3. Flight Request Form

#### Trip Type Toggle
- Tabs or radio buttons: **One Way** | **Return** | **Multi-City**
- Selecting "Return" shows return date field
- Selecting "Multi-City" shows note: "Use the special requests field to list all legs"

#### Form Fields
| Field | Type | Required |
|-------|------|----------|
| Origin (city or airport) | Text input | Yes |
| Destination (city or airport) | Text input | Yes |
| Departure Date | Date picker | Yes |
| Return Date | Date picker | Only if Return |
| Cabin Class | Select: Economy / Premium Economy / Business / First | Yes |
| Number of Passengers | Number (1–20) | Yes |
| Preferred Airline | Text input | No |
| Special Requests | Textarea | No |
| First Name | Text | Yes |
| Last Name | Text | Yes |
| Email | Email | Yes |
| Phone | Tel | Yes |

- Submit button: "Request Flight Quote"

### 4. Why Book With Us
- 3 trust columns: Best Price Search, Flexible Options, Expert Support

---

## Components
- `PageHeader`
- `TripTypeToggle`
- `FlightRequestForm`
- `TrustColumns`

---

## User Flow
```
/flights → Select trip type → Fill form → Submit →
Success message → Agent receives email notification
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Submit form | `POST /api/flight-requests` | `flight_requests` |

### `POST /api/flight-requests` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "trip_type": "one_way|return|multi_city",
  "origin": "string",
  "destination": "string",
  "departure_date": "YYYY-MM-DD",
  "return_date": "YYYY-MM-DD (optional)",
  "cabin_class": "economy|premium_economy|business|first",
  "num_passengers": "number",
  "preferred_airline": "string (optional)",
  "special_requests": "string (optional)"
}
```

---

## Special Rules / Notes
- Return date field is hidden when trip type is "One Way"
- Show inline success message on submit — do not redirect
- Trigger email notification to agent
- Validate that return date is after departure date
- `customer_id` may be null for guest submissions
