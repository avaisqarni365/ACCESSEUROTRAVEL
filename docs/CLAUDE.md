# docs/ — AI Context Guide

This file explains what is in the `docs/` folder and how to use it when working on AccessEuroTravel.

---

## What This Folder Contains

| File | Purpose |
|------|---------|
| `PROJECT_PLAN.md` | Full service list, site structure, tech stack, design guidelines, and phase roadmap |
| `DATABASE_DESIGN.md` | MySQL schema — all 14 tables with full `CREATE TABLE` SQL |
| `TASKS.md` | Phase-by-phase development checklist |
| `HOME_PAGE.md` | Layout, components, and data requirements for the home page |
| `VISA_ASSISTANCE_PAGE.md` | Visa request form spec, API payload, and flow |
| `FLIGHTS_PAGE.md` | Flight request form spec, trip type logic, and flow |
| `HOTELS_PAGE.md` | 3-step hotel booking wizard spec — the canonical source for the hotel flow |
| `TRAVEL_PACKAGES_PAGE.md` | Package grid, filter, detail modal, and request form spec |
| `BUSINESS_TRAVEL_PAGE.md` | Corporate inquiry form and benefits section spec |
| `ABOUT_US_PAGE.md` | Static page layout and content structure |
| `CONTACT_PAGE.md` | Enquiry form, FAQ, WhatsApp link, and lead capture spec |
| `LOGIN_PAGE.md` | Customer login + registration tab spec |

---

## How to Use These Docs

Before building or editing any page:
1. Read the corresponding `*_PAGE.md` file in this folder
2. Cross-reference `DATABASE_DESIGN.md` for the exact table and column names
3. Check `TASKS.md` to confirm the feature is in scope for the current phase

---

## Critical Rules (never override without updating these docs)

- **Hotel booking order is fixed:** Country → City → Hotel Details → Food add-on (optional)
- **Food is not a service.** It is an optional add-on inside the hotel booking wizard only. It must not appear anywhere else on the site.
- **All monetary values** use `DECIMAL(10,2)` with an explicit `currency` column (default `EUR`)
- **All booking requests are agent-reviewed** before confirmation — there is no live inventory or instant booking in Phase 1
- **Guest submissions** (not logged in) are allowed for all request forms; `customer_id` may be null

---

## Keeping Docs Up to Date

When you make a decision that changes a page's layout, flow, or data contract:
1. Update the relevant `*_PAGE.md` file
2. Update `DATABASE_DESIGN.md` if the schema changes
3. Update `TASKS.md` to mark completed tasks and add new ones
4. Save any reusable prompt or pattern to `.claude/prompts/`
