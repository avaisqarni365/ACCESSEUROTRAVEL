# Home Page

## Purpose
The landing page for AccessEuroTravel. Its job is to immediately communicate what the service offers, build trust, and direct visitors to the right service in as few clicks as possible.

## URL
`/`

## Target Users
- First-time visitors exploring travel services
- Returning customers looking to start a new booking
- Business travellers evaluating corporate travel options

---

## Layout & Sections

### 1. Hero Banner
- Full-width background image (European city or landmark)
- Headline: e.g. "Your Gateway to Europe"
- Subheadline: 1-line description of services
- Two CTA buttons: **Explore Services** (scrolls down) | **Contact Us** (→ `/contact`)

### 2. Services Grid
Six equal cards, one per service:

| Card | Icon | Link |
|------|------|------|
| Visa Assistance | Passport icon | `/visa` |
| Flight Booking | Plane icon | `/flights` |
| Hotel Booking | Bed icon | `/hotels` |
| Travel Packages | Globe icon | `/packages` |
| Business Travel | Briefcase icon | `/business` |
| Contact Us | Chat icon | `/contact` |

Each card: icon + title + one-line description + arrow link.

### 3. Featured Packages
- 3-column grid of package cards (CMS-driven, Phase 2)
- Each card: image, destination, duration, price from, CTA button
- Phase 1: use static placeholder cards

### 4. Why Choose Us
- 3–4 trust signal columns: e.g. "10+ Years Experience", "Schengen Experts", "24/7 Support", "500+ Happy Travellers"
- Icon + stat + short label per column

### 5. Testimonials
- 3 customer quotes in card format
- Name, country, star rating
- Static content

### 6. Call-to-Action Strip
- Background colour (brand blue or gold)
- Text: e.g. "Ready to plan your European trip?"
- Button: **Get Started** → `/contact`

---

## Components
- `HeroBanner`
- `ServiceCard`
- `ServicesGrid`
- `PackageCard`
- `PackagesGrid`
- `TrustSignals`
- `TestimonialCard`
- `TestimonialsSection`
- `CtaStrip`

---

## User Flow
```
Land on / → Read hero → Click service card → Arrive on service page
                      → Click "Explore Services" → Scroll to ServicesGrid
                      → Click "Get Started" → /contact
```

---

## Data Requirements
| Source | Used for |
|--------|----------|
| Static / hardcoded | Services grid, trust signals, testimonials |
| CMS (Phase 2) | Featured packages |

No database reads on this page in Phase 1.

---

## Special Rules / Notes
- Hero image must be high quality and compressed (`next/image`)
- Services grid must be fully visible above the fold on desktop
- Page must be fully responsive — stack to single column on mobile
- No food section anywhere on this page
