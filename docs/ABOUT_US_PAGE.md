# About Us Page

## Purpose
Build trust and credibility with visitors by telling the company story, introducing the team, and highlighting key differentiators.

## URL
`/about`

## Target Users
- First-time visitors evaluating whether to trust the service
- Returning users curious about the company
- Potential business partners or corporate clients

---

## Layout & Sections

### 1. Page Header
- Title: "About Us"
- Subtitle: "Your trusted European travel partner"
- Breadcrumb: Home → About Us

### 2. Our Story
- 2-column layout: text left, image right (European office or team photo)
- 2–3 short paragraphs covering:
  - When and why the company was founded
  - The problem it solves
  - What makes it different

### 3. Our Mission
- Centred, bold statement (e.g. "Making Europe accessible to everyone")
- 1–2 lines of supporting text
- Light background colour block to make it stand out

### 4. Key Stats / Trust Signals
4-column row:
- Years in business
- Countries covered
- Happy customers
- Visa applications processed

Each: large number + label.

### 5. Meet the Team
- 3–4 team member cards
- Each: photo, name, role, 1-line bio
- Static content (no CMS needed)

### 6. Our Values
- 3-column cards:
  - Transparency
  - Reliability
  - Customer First

### 7. CTA Strip
- "Ready to start your journey?"
- Button: "Explore Services" → scrolls to home services grid or → `/`

---

## Components
- `PageHeader`
- `OurStory`
- `MissionStatement`
- `StatsRow`
- `TeamCard`
- `TeamGrid`
- `ValuesGrid`
- `CtaStrip`

---

## User Flow
```
/about → Read story → Scan team → Click CTA → /contact or /
```

---

## Data Requirements
None. This is a fully static page.

---

## Special Rules / Notes
- All images must use `next/image` with alt text
- Keep copy concise — visitors scan, they don't read
- No booking forms on this page
- Mobile: all multi-column sections stack to single column
