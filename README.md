# Micro-Lease 🛠️

> **Hyper-local neighborhood tool and equipment rental platform.**

Micro-Lease allows users to list idle household equipment (e.g., pressure washers, carpet cleaners, lawn aerators) for daily rental, while neighbors rent them nearby instead of purchasing expensive items they rarely use.

---

## 1. Core User Flows & UX

### Lender Journey (Listing an Item)
1. **Snap & Title:** Take 2–4 photos directly in-app, enter a title, and select a category (*Lawn & Garden*, *Power Tools*, etc.).
2. **Set Pricing & Deposit:** Set daily rate (e.g., $25/day) and security deposit limit (e.g., $100).
3. **Geofence Location:** Set pickup location on a map (masked to a ~500m area until booking confirmation).
4. **Publish Listing:** Choose availability calendar and publish in under 90 seconds.

### Renter Journey (Booking to Return)
1. **Discover & Filter:** Search by proximity and availability; view ratings, reviews, and specs.
2. **Reserve & Hold:** Select dates, complete identity verification (Stripe Identity), and authorize rental fee + security deposit hold.
3. **Pick-Up Verification:** Meet Lender, scan Lender's dynamic QR code, and submit 4 timestamped condition photos.
4. **Return & Handshake:** Drop off item. Lender inspects, scans Renter's QR code, submits return photos, releasing the deposit hold.

### Verification Process
* **Dynamic Handshake QR:** Receiving party scans dynamic QR code to activate or close booking status in real time.
* **4-Angle Photo Proof:** Mandatory 4-photo capture (Front, Back, Serial/Model, Working Indicator) at pickup and return.
* **Metadata Hash:** Photo metadata (GPS, UTC timestamp, image hashes) attached to `Booking` record for dispute proof.

---

## 2. Database Schema (PostgreSQL / Supabase)

```sql
CREATE EXTENSION IF NOT EXISTS postgis;

-- USERS TABLE
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    full_name TEXT NOT NULL,
    phone_number TEXT UNIQUE NOT NULL,
    stripe_customer_id TEXT,
    stripe_account_id TEXT,
    is_identity_verified BOOLEAN DEFAULT FALSE,
    average_rating NUMERIC(3,2) DEFAULT 0.00,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- ITEMS TABLE
CREATE TABLE items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    lender_id UUID REFERENCES users(id) ON DELETE CASCADE,
    title TEXT NOT NULL,
    description TEXT,
    category TEXT NOT NULL,
    daily_price_cents INT NOT NULL,
    deposit_amount_cents INT NOT NULL,
    condition_grade TEXT CHECK (condition_grade IN ('Like New', 'Good', 'Fair')),
    location GEOGRAPHY(POINT, 4326) NOT NULL,
    approx_address TEXT NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- BOOKINGS TABLE
CREATE TYPE booking_status AS ENUM ('pending', 'confirmed', 'active', 'completed', 'disputed', 'cancelled');

CREATE TABLE bookings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    item_id UUID REFERENCES items(id),
    renter_id UUID REFERENCES users(id),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    rental_fee_cents INT NOT NULL,
    deposit_hold_cents INT NOT NULL,
    stripe_payment_intent_id TEXT,
    stripe_deposit_hold_id TEXT,
    status booking_status DEFAULT 'pending',
    pickup_timestamp TIMESTAMPTZ,
    return_timestamp TIMESTAMPTZ,
    pickup_photos TEXT[],
    return_photos TEXT[],
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- REVIEWS TABLE
CREATE TABLE reviews (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    booking_id UUID REFERENCES bookings(id) ON DELETE CASCADE,
    reviewer_id UUID REFERENCES users(id),
    reviewee_id UUID REFERENCES users(id),
    rating INT CHECK (rating >= 1 AND rating <= 5),
    comment TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_items_location ON items USING GIST(location);
CREATE INDEX idx_bookings_dates ON bookings(item_id, start_date, end_date);

