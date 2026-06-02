# Login Page

## Purpose
Allow existing customers to sign in to their account and new users to register. Provides access to booking history, request status tracking, and profile management.

## URL
`/login` (login tab, default)
`/login?tab=register` (registration tab)
`/account` (redirect destination after login)

## Target Users
- Registered customers checking booking status
- New customers creating an account after a booking request
- Agents are not managed from this page (they use `/admin/login`)

---

## Layout & Sections

### 1. Page Header
- Centred layout (narrow card, max-width ~480px)
- Logo above the card
- Tab switcher: **Sign In** | **Register**

---

### 2. Sign In Tab

| Field | Type | Required |
|-------|------|----------|
| Email | Email | Yes |
| Password | Password | Yes |
| Remember me | Checkbox | No |

- Submit button: "Sign In"
- Forgot password link → `/forgot-password` (Phase 4)
- Link: "Don't have an account? Register"

On success: redirect to `/account`
On error: show inline error message ("Invalid email or password")

---

### 3. Register Tab

| Field | Type | Required |
|-------|------|----------|
| First Name | Text | Yes |
| Last Name | Text | Yes |
| Email | Email | Yes |
| Phone | Tel | No |
| Password | Password | Yes (min 8 chars) |
| Confirm Password | Password | Yes |

- Submit button: "Create Account"
- Link: "Already have an account? Sign In"

On success:
- Create entry in `users` table (role: customer — handled via separate customer auth flow)
- Link to existing `customers` record by email if one exists
- Redirect to `/account`

On error: show inline validation messages

---

### 4. Social / Divider (Phase 4)
- "Or continue with" divider
- Google sign-in button (NextAuth Google provider)

---

## Components
- `AuthCard`
- `TabSwitcher`
- `LoginForm`
- `RegisterForm`
- `FormFieldError`

---

## User Flow
```
/login (Sign In tab)
  → Enter email + password → Submit → /account

/login?tab=register (Register tab)
  → Fill registration form → Submit → /account
  → If email matches existing customer record → accounts are linked
```

---

## Data Requirements

| Action | API Route | DB Table |
|--------|-----------|----------|
| Sign in | NextAuth `POST /api/auth/signin` | `users` |
| Register | `POST /api/auth/register` | `users` + `customers` |

### `POST /api/auth/register` payload
```json
{
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string (optional)",
  "password": "string"
}
```

---

## Special Rules / Notes
- Passwords must be hashed with bcrypt before storing (never store plain text)
- `/login` redirects to `/account` if user is already authenticated
- `/admin/login` is a completely separate page — regular customers never land there
- Tab state is preserved in URL query param (`?tab=register`) for direct linking
- "Remember me" sets a longer session duration via NextAuth session config
- On registration, check if a `customers` record with the same email already exists — if so, set `user_id` on that record to link them
