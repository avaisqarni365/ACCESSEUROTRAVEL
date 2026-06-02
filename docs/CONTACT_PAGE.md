# Contact Page

## Purpose
Capture general enquiries, convert visitors into leads, and provide multiple ways to reach the team (form, WhatsApp, email, FAQ).

## URL
`/contact`

## Target Users
- Visitors with questions before committing to a service
- Existing customers needing support
- Business enquiries not covered by the business travel form

---

## Layout & Sections

### 1. Page Header
- Title: "Contact Us"
- Subtitle: "We're here to help — get in touch and we'll respond within 24 hours"
- Breadcrumb: Home → Contact Us

### 2. Contact Options Bar
3 quick-contact blocks (above the form):
- **WhatsApp**: icon + "Chat on WhatsApp" → opens WhatsApp link
- **Email**: icon + email address → `mailto:` link
- **Phone**: icon + phone number → `tel:` link

### 3. General Enquiry Form
| Field | Type | Required |
|-------|------|----------|
| First Name | Text | Yes |
| Last Name | Text | Yes |
| Email | Email | Yes |
| Phone | Tel | No |
| Service Interest | Select: Visa / Flights / Hotels / Packages / Business Travel / General | Yes |
| Message | Textarea | Yes |
| Submit button | — | — |

Label: "Send Message"

On submit:
- Insert into `leads` table (status: `new`)
- Insert into `messages` table (sender_type: `guest`, related_entity_type: null)
- Show inline success message

### 4. FAQ Accordion
8–10 common questions, grouped by topic:
- **Visa**: How long does it take? What documents do I need?
- **Flights**: Do you issue tickets directly? What if my flight is cancelled?
- **Hotels**: Can I request a specific hotel? Is breakfast included?
- **General**: How does the booking process work? Is there a fee to enquire?

### 5. Office / Location Info
- Office address (if applicable)
- Working hours
- Map embed (optional, Phase 2)

---

## Components
- `PageHeader`
- `ContactOptionsBar`
- `ContactForm`
- `FaqAccordion`
- `OfficeInfo`

---

## User Flow
```
/contact
  → Read contact options → Click WhatsApp → Opens WhatsApp chat
  OR
  → Fill enquiry form → Submit → Success message
  OR
  → Expand FAQ items → Find answer → No form needed
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Submit form | `POST /api/leads` | `leads` + `messages` |

### `POST /api/leads` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string (optional)",
  "service_interest": "visa|flight|hotel|package|business_travel|general",
  "message": "string",
  "source": "website"
}
```

---

## Special Rules / Notes
- Show inline success message after submit — do not redirect
- Trigger email notification to agent with lead details
- WhatsApp link must use the international format: `https://wa.me/<number>`
- FAQ content is static in Phase 1; can be CMS-driven in Phase 2
- Form submissions go to `leads` (not `visa_requests` or other request tables) — the contact page is for general enquiries only
