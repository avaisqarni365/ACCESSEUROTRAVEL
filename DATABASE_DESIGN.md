# AccessEuroTravel — Database Design (MySQL)

## Conventions
- All primary keys: `BIGINT UNSIGNED AUTO_INCREMENT`
- All timestamps: `DATETIME DEFAULT CURRENT_TIMESTAMP`
- Soft deletes via `deleted_at` where noted
- `status` columns use `ENUM` with explicit allowed values
- Foreign keys are indexed; all FKs enforce referential integrity

---

## Entity Relationship Summary

```
users ──────────────────────── (agents / admins)
customers ──── user_id ──────── users
countries ◄─── cities
cities ◄─────── hotel_requests

customers ──► hotel_requests
customers ──► visa_requests
customers ──► flight_requests
customers ──► package_requests
customers ──► business_travel_requests
customers ──► leads
customers ──► documents
customers ──► invoices

invoices ───► payments
documents ──► (any entity via polymorphic entity_type / entity_id)
messages ───► (any entity via polymorphic related_entity_type / related_entity_id)
```

---

## Tables

### 1. `users`
System users — agents and admins only. Customers have a separate table.

```sql
CREATE TABLE users (
    id              BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    first_name      VARCHAR(100)    NOT NULL,
    last_name       VARCHAR(100)    NOT NULL,
    email           VARCHAR(255)    NOT NULL UNIQUE,
    password_hash   VARCHAR(255)    NOT NULL,
    role            ENUM('admin', 'agent') NOT NULL DEFAULT 'agent',
    is_active       TINYINT(1)      NOT NULL DEFAULT 1,
    last_login_at   DATETIME,
    created_at      DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at      DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at      DATETIME
);
```

---

### 2. `customers`
Registered customers. `user_id` is NULL for guest/lead-converted customers who have not created an account yet.

```sql
CREATE TABLE customers (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id             BIGINT UNSIGNED,                          -- NULL until account created
    first_name          VARCHAR(100)    NOT NULL,
    last_name           VARCHAR(100)    NOT NULL,
    email               VARCHAR(255)    NOT NULL UNIQUE,
    phone               VARCHAR(30),
    nationality         VARCHAR(100),
    passport_number     VARCHAR(50),
    passport_expiry     DATE,
    date_of_birth       DATE,
    address             TEXT,
    notes               TEXT,
    created_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at          DATETIME,

    CONSTRAINT fk_customers_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_customers_email (email),
    INDEX idx_customers_user_id (user_id)
);
```

---

### 3. `countries`
Reference table for all countries.

```sql
CREATE TABLE countries (
    id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name        VARCHAR(100)    NOT NULL,
    iso_code    CHAR(2)         NOT NULL UNIQUE,   -- ISO 3166-1 alpha-2
    is_active   TINYINT(1)      NOT NULL DEFAULT 1,

    INDEX idx_countries_iso (iso_code)
);
```

---

### 4. `cities`
Cities linked to a country. Used in hotel booking flow (step 2).

```sql
CREATE TABLE cities (
    id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    country_id  BIGINT UNSIGNED NOT NULL,
    name        VARCHAR(150)    NOT NULL,
    is_active   TINYINT(1)      NOT NULL DEFAULT 1,

    CONSTRAINT fk_cities_country FOREIGN KEY (country_id) REFERENCES countries(id) ON DELETE CASCADE,
    INDEX idx_cities_country (country_id)
);
```

---

### 5. `hotel_requests`
Hotel booking requests. Follows the fixed flow: country → city → hotel details → food add-on (optional).

```sql
CREATE TABLE hotel_requests (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id         BIGINT UNSIGNED NOT NULL,
    country_id          BIGINT UNSIGNED NOT NULL,
    city_id             BIGINT UNSIGNED NOT NULL,
    hotel_name          VARCHAR(255),
    check_in            DATE            NOT NULL,
    check_out           DATE            NOT NULL,
    room_type           ENUM('single', 'double', 'twin', 'suite', 'family') NOT NULL,
    num_guests          TINYINT UNSIGNED NOT NULL DEFAULT 1,
    food_addon          ENUM('none', 'breakfast', 'half_board', 'full_board') NOT NULL DEFAULT 'none',
    special_requests    TEXT,
    status              ENUM('pending', 'reviewing', 'quoted', 'confirmed', 'cancelled') NOT NULL DEFAULT 'pending',
    agent_id            BIGINT UNSIGNED,
    agent_notes         TEXT,
    quoted_price        DECIMAL(10,2),
    currency            CHAR(3)         DEFAULT 'EUR',
    created_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_hotel_req_customer FOREIGN KEY (customer_id) REFERENCES customers(id),
    CONSTRAINT fk_hotel_req_country  FOREIGN KEY (country_id)  REFERENCES countries(id),
    CONSTRAINT fk_hotel_req_city     FOREIGN KEY (city_id)     REFERENCES cities(id),
    CONSTRAINT fk_hotel_req_agent    FOREIGN KEY (agent_id)    REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_hotel_req_customer (customer_id),
    INDEX idx_hotel_req_status   (status)
);
```

---

### 6. `visa_requests`

```sql
CREATE TABLE visa_requests (
    id                      BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id             BIGINT UNSIGNED NOT NULL,
    destination_country_id  BIGINT UNSIGNED NOT NULL,
    visa_type               ENUM('tourist', 'business', 'student', 'transit', 'family', 'work') NOT NULL,
    travel_date             DATE,
    duration_days           SMALLINT UNSIGNED,
    purpose                 TEXT,
    status                  ENUM('pending', 'reviewing', 'documents_requested', 'submitted', 'approved', 'rejected', 'cancelled') NOT NULL DEFAULT 'pending',
    agent_id                BIGINT UNSIGNED,
    agent_notes             TEXT,
    created_at              DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at              DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_visa_req_customer FOREIGN KEY (customer_id)            REFERENCES customers(id),
    CONSTRAINT fk_visa_req_country  FOREIGN KEY (destination_country_id) REFERENCES countries(id),
    CONSTRAINT fk_visa_req_agent    FOREIGN KEY (agent_id)               REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_visa_req_customer (customer_id),
    INDEX idx_visa_req_status   (status)
);
```

---

### 7. `flight_requests`

```sql
CREATE TABLE flight_requests (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id         BIGINT UNSIGNED NOT NULL,
    trip_type           ENUM('one_way', 'return', 'multi_city') NOT NULL,
    origin              VARCHAR(100)    NOT NULL,
    destination         VARCHAR(100)    NOT NULL,
    departure_date      DATE            NOT NULL,
    return_date         DATE,
    cabin_class         ENUM('economy', 'premium_economy', 'business', 'first') NOT NULL DEFAULT 'economy',
    num_passengers      TINYINT UNSIGNED NOT NULL DEFAULT 1,
    preferred_airline   VARCHAR(100),
    special_requests    TEXT,
    status              ENUM('pending', 'reviewing', 'quoted', 'confirmed', 'cancelled') NOT NULL DEFAULT 'pending',
    agent_id            BIGINT UNSIGNED,
    agent_notes         TEXT,
    quoted_price        DECIMAL(10,2),
    currency            CHAR(3)         DEFAULT 'EUR',
    created_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_flight_req_customer FOREIGN KEY (customer_id) REFERENCES customers(id),
    CONSTRAINT fk_flight_req_agent    FOREIGN KEY (agent_id)    REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_flight_req_customer (customer_id),
    INDEX idx_flight_req_status   (status)
);
```

---

### 8. `package_requests`
Requests for pre-built or custom travel packages.

```sql
CREATE TABLE package_requests (
    id                      BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id             BIGINT UNSIGNED NOT NULL,
    package_name            VARCHAR(255),              -- NULL if fully custom
    destination_country_id  BIGINT UNSIGNED,
    destination_city_id     BIGINT UNSIGNED,
    start_date              DATE,
    end_date                DATE,
    num_travelers           TINYINT UNSIGNED NOT NULL DEFAULT 1,
    budget                  DECIMAL(10,2),
    currency                CHAR(3)          DEFAULT 'EUR',
    includes_flights        TINYINT(1)       NOT NULL DEFAULT 1,
    includes_hotels         TINYINT(1)       NOT NULL DEFAULT 1,
    includes_transfers      TINYINT(1)       NOT NULL DEFAULT 0,
    notes                   TEXT,
    status                  ENUM('pending', 'reviewing', 'quoted', 'confirmed', 'cancelled') NOT NULL DEFAULT 'pending',
    agent_id                BIGINT UNSIGNED,
    agent_notes             TEXT,
    quoted_price            DECIMAL(10,2),
    created_at              DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at              DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_pkg_req_customer FOREIGN KEY (customer_id)            REFERENCES customers(id),
    CONSTRAINT fk_pkg_req_country  FOREIGN KEY (destination_country_id) REFERENCES countries(id) ON DELETE SET NULL,
    CONSTRAINT fk_pkg_req_city     FOREIGN KEY (destination_city_id)    REFERENCES cities(id)    ON DELETE SET NULL,
    CONSTRAINT fk_pkg_req_agent    FOREIGN KEY (agent_id)               REFERENCES users(id)     ON DELETE SET NULL,
    INDEX idx_pkg_req_customer (customer_id),
    INDEX idx_pkg_req_status   (status)
);
```

---

### 9. `business_travel_requests`

```sql
CREATE TABLE business_travel_requests (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id         BIGINT UNSIGNED,              -- NULL if not yet a registered customer
    company_name        VARCHAR(255)    NOT NULL,
    contact_name        VARCHAR(200)    NOT NULL,
    contact_email       VARCHAR(255)    NOT NULL,
    contact_phone       VARCHAR(30),
    num_travelers       TINYINT UNSIGNED NOT NULL DEFAULT 1,
    destination         VARCHAR(255),
    travel_start        DATE,
    travel_end          DATE,
    requirements        TEXT,
    status              ENUM('pending', 'reviewing', 'quoted', 'confirmed', 'cancelled') NOT NULL DEFAULT 'pending',
    agent_id            BIGINT UNSIGNED,
    agent_notes         TEXT,
    quoted_price        DECIMAL(10,2),
    currency            CHAR(3)         DEFAULT 'EUR',
    created_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_biz_req_customer FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE SET NULL,
    CONSTRAINT fk_biz_req_agent    FOREIGN KEY (agent_id)    REFERENCES users(id)     ON DELETE SET NULL,
    INDEX idx_biz_req_customer (customer_id),
    INDEX idx_biz_req_status   (status)
);
```

---

### 10. `leads`
Prospective customers who submitted an inquiry but have not yet booked.

```sql
CREATE TABLE leads (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    first_name          VARCHAR(100)    NOT NULL,
    last_name           VARCHAR(100),
    email               VARCHAR(255)    NOT NULL,
    phone               VARCHAR(30),
    service_interest    ENUM('visa', 'flight', 'hotel', 'package', 'business_travel', 'general') NOT NULL DEFAULT 'general',
    source              ENUM('website', 'whatsapp', 'email', 'referral', 'social_media', 'other') NOT NULL DEFAULT 'website',
    notes               TEXT,
    status              ENUM('new', 'contacted', 'converted', 'lost') NOT NULL DEFAULT 'new',
    converted_customer_id BIGINT UNSIGNED,            -- set when lead becomes a customer
    agent_id            BIGINT UNSIGNED,
    created_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_leads_customer FOREIGN KEY (converted_customer_id) REFERENCES customers(id) ON DELETE SET NULL,
    CONSTRAINT fk_leads_agent    FOREIGN KEY (agent_id)              REFERENCES users(id)     ON DELETE SET NULL,
    INDEX idx_leads_status (status),
    INDEX idx_leads_email  (email)
);
```

---

### 11. `documents`
Uploaded files attached to any request type (polymorphic).

```sql
CREATE TABLE documents (
    id              BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id     BIGINT UNSIGNED NOT NULL,
    entity_type     ENUM('visa_request', 'hotel_request', 'flight_request', 'package_request', 'business_travel_request', 'invoice', 'general') NOT NULL,
    entity_id       BIGINT UNSIGNED NOT NULL,
    document_type   VARCHAR(100)    NOT NULL,   -- e.g. 'passport_copy', 'bank_statement', 'hotel_confirmation'
    file_name       VARCHAR(255)    NOT NULL,
    file_path       VARCHAR(500)    NOT NULL,
    mime_type       VARCHAR(100),
    file_size_kb    INT UNSIGNED,
    uploaded_by     BIGINT UNSIGNED,            -- user (agent) or NULL if customer self-upload
    created_at      DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_docs_customer    FOREIGN KEY (customer_id)  REFERENCES customers(id),
    CONSTRAINT fk_docs_uploaded_by FOREIGN KEY (uploaded_by)  REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_docs_entity (entity_type, entity_id),
    INDEX idx_docs_customer (customer_id)
);
```

---

### 12. `invoices`

```sql
CREATE TABLE invoices (
    id              BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id     BIGINT UNSIGNED NOT NULL,
    entity_type     ENUM('hotel_request', 'flight_request', 'visa_request', 'package_request', 'business_travel_request') NOT NULL,
    entity_id       BIGINT UNSIGNED NOT NULL,
    invoice_number  VARCHAR(50)     NOT NULL UNIQUE,   -- e.g. INV-2026-00001
    amount          DECIMAL(10,2)   NOT NULL,
    tax_amount      DECIMAL(10,2)   NOT NULL DEFAULT 0.00,
    total_amount    DECIMAL(10,2)   NOT NULL,
    currency        CHAR(3)         NOT NULL DEFAULT 'EUR',
    status          ENUM('draft', 'sent', 'partially_paid', 'paid', 'cancelled') NOT NULL DEFAULT 'draft',
    due_date        DATE,
    notes           TEXT,
    issued_at       DATETIME,
    paid_at         DATETIME,
    created_at      DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at      DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_invoices_customer FOREIGN KEY (customer_id) REFERENCES customers(id),
    INDEX idx_invoices_customer (customer_id),
    INDEX idx_invoices_entity   (entity_type, entity_id),
    INDEX idx_invoices_status   (status)
);
```

---

### 13. `payments`

```sql
CREATE TABLE payments (
    id                  BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    invoice_id          BIGINT UNSIGNED NOT NULL,
    amount              DECIMAL(10,2)   NOT NULL,
    currency            CHAR(3)         NOT NULL DEFAULT 'EUR',
    method              ENUM('stripe', 'bank_transfer', 'cash', 'paypal', 'other') NOT NULL,
    transaction_id      VARCHAR(255),              -- Stripe payment intent ID or bank reference
    status              ENUM('pending', 'completed', 'failed', 'refunded') NOT NULL DEFAULT 'pending',
    paid_at             DATETIME,
    notes               TEXT,
    created_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at          DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_payments_invoice FOREIGN KEY (invoice_id) REFERENCES invoices(id),
    INDEX idx_payments_invoice      (invoice_id),
    INDEX idx_payments_status       (status),
    INDEX idx_payments_transaction  (transaction_id)
);
```

---

### 14. `messages`
Customer inquiries, agent replies, and internal notes. Polymorphic sender/recipient and optional entity link.

```sql
CREATE TABLE messages (
    id                      BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    sender_type             ENUM('customer', 'agent', 'guest') NOT NULL,
    sender_customer_id      BIGINT UNSIGNED,    -- set if sender_type = 'customer'
    sender_user_id          BIGINT UNSIGNED,    -- set if sender_type = 'agent'
    sender_guest_email      VARCHAR(255),       -- set if sender_type = 'guest'
    recipient_type          ENUM('customer', 'agent') NOT NULL,
    recipient_customer_id   BIGINT UNSIGNED,
    recipient_user_id       BIGINT UNSIGNED,
    subject                 VARCHAR(255),
    body                    TEXT            NOT NULL,
    is_read                 TINYINT(1)      NOT NULL DEFAULT 0,
    related_entity_type     ENUM('visa_request', 'hotel_request', 'flight_request', 'package_request', 'business_travel_request', 'invoice', 'lead'),
    related_entity_id       BIGINT UNSIGNED,
    created_at              DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_msg_sender_customer    FOREIGN KEY (sender_customer_id)    REFERENCES customers(id) ON DELETE SET NULL,
    CONSTRAINT fk_msg_sender_user        FOREIGN KEY (sender_user_id)        REFERENCES users(id)     ON DELETE SET NULL,
    CONSTRAINT fk_msg_recipient_customer FOREIGN KEY (recipient_customer_id) REFERENCES customers(id) ON DELETE SET NULL,
    CONSTRAINT fk_msg_recipient_user     FOREIGN KEY (recipient_user_id)     REFERENCES users(id)     ON DELETE SET NULL,
    INDEX idx_messages_sender_customer    (sender_customer_id),
    INDEX idx_messages_recipient_customer (recipient_customer_id),
    INDEX idx_messages_related_entity     (related_entity_type, related_entity_id),
    INDEX idx_messages_is_read            (is_read)
);
```

---

## Seed Data Notes

```sql
-- Seed example countries
INSERT INTO countries (name, iso_code) VALUES
('France',      'FR'),
('Germany',     'DE'),
('Italy',       'IT'),
('Spain',       'ES'),
('Netherlands', 'NL'),
('Portugal',    'PT'),
('Greece',      'GR'),
('Austria',     'AT'),
('Switzerland', 'CH'),
('Belgium',     'BE');

-- Seed example cities (France)
INSERT INTO cities (country_id, name) VALUES
(1, 'Paris'), (1, 'Lyon'), (1, 'Nice'), (1, 'Marseille');
```

---

## Notes

- **Food add-on** lives on `hotel_requests.food_addon` — not a separate table or service
- **Polymorphic links** (`entity_type` + `entity_id`) are used on `documents`, `invoices`, and `messages` to avoid a separate join table per request type
- **Invoice numbering** (`INV-YYYY-#####`) should be generated at the application layer before insert
- **Soft deletes** on `users` and `customers` preserve referential integrity while hiding records from the UI
- All monetary amounts use `DECIMAL(10,2)` with an explicit `currency` column (default `EUR`)
