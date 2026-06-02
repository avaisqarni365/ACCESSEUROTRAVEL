# Visa Assistance Page

## Purpose
Help users understand the visa process for their chosen European destination and submit a visa assistance request to an agent.

## URL
`/visa`

## Target Users
- Individuals applying for Schengen or national visas
- Families or groups needing multi-applicant guidance
- Business travellers needing short-stay business visas

---

## Layout & Sections

### 1. Page Header
- Title: "Visa Assistance"
- Subtitle: brief description (e.g. "Let our experts guide you through your European visa application")
- Breadcrumb: Home → Visa Assistance

### 2. How It Works
Step-by-step process explanation (horizontal on desktop, vertical on mobile):
1. Submit your request
2. Agent reviews your case
3. Receive document checklist
4. We guide you through the application
5. Travel with confidence

### 3. Visa Types Explained
Accordion or card grid explaining:
- Tourist Visa
- Business Visa
- Student Visa
- Transit Visa
- Family Reunification Visa
- Work Visa

### 4. Visa Request Form
Fields (all required unless marked optional):
- First Name
- Last Name
- Email
- Phone
- Destination Country (dropdown from `countries` table)
- Visa Type (dropdown: tourist / business / student / transit / family / work)
- Intended Travel Date (date picker)
- Duration of Stay (number input, days)
- Purpose of Visit (textarea, optional)
- Submit button: "Request Visa Assistance"

### 5. FAQ
Accordion with common visa questions:
- How long does the process take?
- What documents do I need?
- Can I apply for multiple countries?
- What is a Schengen visa?

---

## Components
- `PageHeader`
- `HowItWorks` (step indicator)
- `VisaTypeCards`
- `VisaRequestForm`
- `FaqAccordion`

---

## User Flow
```
/visa → Read overview → Expand visa type info → Fill form →
Submit → Success message shown → Email sent to agent
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Load countries | `GET /api/countries` | `countries` |
| Submit form | `POST /api/visa-requests` | `visa_requests` |

### `POST /api/visa-requests` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "destination_country_id": "number",
  "visa_type": "tourist|business|student|transit|family|work",
  "travel_date": "YYYY-MM-DD",
  "duration_days": "number",
  "purpose": "string (optional)"
}
```

---

## Special Rules / Notes
- On successful submission: show inline success message (do not redirect)
- Trigger email notification to agent with full request details
- Validate email format and phone number before submit
- Countries dropdown must only show `is_active = 1` records
- A `customer_id` may be null for guest submissions (logged-out users)
