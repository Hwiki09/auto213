# Phase 1: Sub-Requirement Document
## Walking Skeleton — Core Marketplace with Bilingual Support

**Version:** 1.0  
**Created:** 2025-12-12  
**Status:** Approved  
**Parent PRD:** [Auto213 PRD.md](./Auto213%20PRD.md)

---

## Definition of Done

> A private user can browse car listings in French or Arabic, create an account, verify their phone via WhatsApp OTP, post a car for sale with 5+ photos, and another user can find that car via search/filter and contact the seller directly.

---

## 1. User Stories

| ID | User Story | Priority | Acceptance Criteria |
|----|------------|----------|---------------------|
| **P1-US01** | As a **first-time user**, I want to **select my preferred language (FR/AR)**, so I can **use the app in my native language**. | 🔴 P0 | 1. Language selection screen on first launch.<br>2. App renders in selected language with proper RTL for Arabic.<br>3. Language persists across sessions. |
| **P1-US02** | As a **guest**, I want to **browse car listings**, so I can **see what's available before signing up**. | 🔴 P0 | 1. Guest can view all active listings.<br>2. Guest can view listing details including photos, specs, price.<br>3. Guest can view seller phone number.<br>4. Guest can tap Call/WhatsApp buttons. |
| **P1-US03** | As a **guest**, I want to **search and filter cars**, so I can **find vehicles matching my criteria**. | 🔴 P0 | 1. Filter by: Make, Model, Year range, Max Mileage, Location (Wilaya/Commune), Price range, Fuel, Gearbox, Accepts Exchange.<br>2. Sort by: Newest, Price (low/high), Year, Mileage.<br>3. "Show X Results" displays count.<br>4. Zero results shows empty state. |
| **P1-US04** | As a **guest**, I want to **create an account**, so I can **post my car for sale**. | 🔴 P0 | 1. Sign up via Google (all platforms) or Apple (iOS only).<br>2. Enter: First name, Last name, Wilaya, Commune.<br>3. Account created in "Unverified" state.<br>4. User redirected to Home. |
| **P1-US05** | As an **unverified user**, I want to **verify my phone number**, so I can **post ads**. | 🔴 P0 | 1. Triggered when user attempts to publish first ad.<br>2. Enter +213 phone number.<br>3. Receive WhatsApp OTP (SMS fallback if WhatsApp fails).<br>4. Enter OTP → Account becomes "Verified". |
| **P1-US06** | As a **verified user**, I want to **post a car for sale**, so I can **find a buyer**. | 🔴 P0 | 1. Fill required fields: Make, Model, Year, Color, Wilaya/Commune, Fuel, Transmission, Mileage, Papers, Bodywork, Description, Price, Price Type, Accepts Exchange toggle.<br>2. Upload minimum 5 photos.<br>3. "Publish" button disabled until 5+ photos.<br>4. Listing goes live immediately (no approval in Phase 1). |
| **P1-US07** | As a **buyer**, I want to **contact a seller**, so I can **inquire about or purchase the car**. | 🔴 P0 | 1. "Call" button copies number to dialer.<br>2. "WhatsApp" button opens WhatsApp with pre-filled message.<br>3. Works for guests (no login required). |
| **P1-US08** | As a **user**, I want to **change my language setting**, so I can **switch between French and Arabic**. | 🟡 P1 | 1. Language toggle in Settings.<br>2. App reloads in new language with proper layout direction. |

---

## 2. Functional Requirements

### 2.1 Authentication & Onboarding

| Requirement | Details |
|-------------|---------|
| **Social Login Providers** | Google Sign-In (iOS + Android), Apple Sign-In (iOS only) |
| **Account Types (Phase 1)** | Private User only (Pro Dealer deferred to Phase 2) |
| **Required Profile Fields** | First name, Last name, Wilaya, Commune |
| **Account States** | `UNVERIFIED` → `VERIFIED` (after phone OTP) |
| **Session Management** | JWT tokens, refresh token flow, secure storage |

### 2.2 Phone Verification (WhatsApp OTP)

| Requirement | Details |
|-------------|---------|
| **Trigger** | User attempts to publish first listing |
| **Phone Format** | +213 prefix, 9 digits (Algerian mobile) |
| **Primary Channel** | WhatsApp Business API |
| **Fallback Channel** | SMS (Twilio/Vonage) if WhatsApp delivery fails |
| **OTP Format** | 6-digit numeric code |
| **OTP Expiry** | 5 minutes |
| **Retry Limit** | 3 attempts, then 15-minute cooldown |

### 2.3 Listing Creation

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| Brand (Marque) | Dropdown | ✅ | From car makes database |
| Model (Modèle) | Dropdown | ✅ | Dependent on Brand |
| Year (Année) | Dropdown | ✅ | 1980–Current Year |
| Color | Dropdown | ✅ | Predefined colors |
| Wilaya | Dropdown | ✅ | From Wilaya database |
| Commune | Dropdown | ✅ | Dependent on Wilaya |
| Fuel (Energie) | Dropdown | ✅ | Essence, Diesel, GPL, Hybrid, Electric |
| Transmission | Dropdown | ✅ | Manuelle, Automatique |
| Mileage (Km) | Number | ✅ | 0–999,999 |
| Papers | Dropdown | ✅ | Carte Grise, Licence, Carte Jaune |
| Bodywork (Sbigha) | Dropdown | ✅ | Original, Partial Repaint, Full Repaint, Accident |
| Description | Text | ✅ | Max 2000 chars |
| Price | Number | ✅ | Min 1 (no "Price on Request") |
| Price Type | Dropdown | ✅ | Fixed, Negotiable, Offered (Atawli) |
| Accepts Exchange | Toggle | ✅ | Boolean |
| Photos | Images | ✅ | Minimum 5, max 20 |

### 2.4 Search & Filter

| Filter | Type | Options |
|--------|------|---------|
| Make | Single-select dropdown | All makes |
| Model | Single-select dropdown | Depends on Make |
| Year | Range (Min–Max) | 1980–Current |
| Max Mileage | Number input | e.g., <150,000 km |
| Wilaya | Single-select dropdown | 58 Wilayas |
| Commune | Single-select dropdown | Depends on Wilaya |
| Price | Range (Min–Max) | Number |
| Fuel | Multi-select | Essence, Diesel, GPL, Hybrid, Electric |
| Gearbox | Multi-select | Manual, Auto |
| Accepts Exchange | Toggle | Yes/No |

| Sort Option | Default |
|-------------|---------|
| Newest First | ✅ Default |
| Price: Low → High | |
| Price: High → Low | |
| Year: Newest → Oldest | |
| Mileage: Low → High | |

### 2.5 Localization (FR + AR)

| Requirement | Details |
|-------------|---------|
| **Languages** | French (FR), Arabic (AR-DZ) |
| **RTL Support** | Full RTL layout for Arabic |
| **String Externalization** | All UI strings in localization files (no hardcoded text) |
| **First Launch** | Language selection screen (mandatory) |
| **Settings** | Language toggle available |
| **Persistence** | Language preference stored locally + synced to user profile |
| **Dynamic Content** | Car makes/models, Wilayas/Communes in both languages |

---

## 3. Technical Specifications

### 3.1 Technology Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | Flutter (iOS + Android) |
| **Backend** | Ktor (Kotlin) |
| **Database** | PostgreSQL |
| **Hosting** | VPS (self-hosted) |
| **OTP Primary** | WhatsApp Business API |
| **OTP Fallback** | Twilio/Vonage SMS |

### 3.2 API Endpoints (Ktor Backend)

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/auth/google` | POST | None | Google OAuth token exchange |
| `/auth/apple` | POST | None | Apple OAuth token exchange |
| `/auth/refresh` | POST | Refresh Token | Refresh access token |
| `/users/me` | GET | JWT | Get current user profile |
| `/users/me` | PUT | JWT | Update user profile (name, location) |
| `/users/me/verify/request` | POST | JWT | Request OTP (phone number in body) |
| `/users/me/verify/confirm` | POST | JWT | Confirm OTP (code in body) |
| `/listings` | GET | None | List/search listings (query params for filters) |
| `/listings/{id}` | GET | None | Get single listing details |
| `/listings` | POST | JWT (Verified) | Create new listing |
| `/uploads/image` | POST | JWT | Upload image, returns URL |
| `/reference/makes` | GET | None | Get car makes |
| `/reference/models/{makeId}` | GET | None | Get models for make |
| `/reference/wilayas` | GET | None | Get all Wilayas |
| `/reference/communes/{wilayaId}` | GET | None | Get communes for Wilaya |
| `/reference/colors` | GET | None | Get color options |

### 3.3 Database Schema (PostgreSQL)

```sql
-- Users table
CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email           VARCHAR(255) UNIQUE,
    first_name      VARCHAR(100) NOT NULL,
    last_name       VARCHAR(100) NOT NULL,
    phone           VARCHAR(20),
    wilaya_id       INT REFERENCES wilayas(id),
    commune_id      INT REFERENCES communes(id),
    auth_provider   VARCHAR(20) CHECK (auth_provider IN ('google', 'apple')),
    auth_provider_id VARCHAR(255),
    status          VARCHAR(20) DEFAULT 'unverified' CHECK (status IN ('unverified', 'verified')),
    language        VARCHAR(5) DEFAULT 'fr' CHECK (language IN ('fr', 'ar')),
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP
);

-- Listings table
CREATE TABLE listings (
    id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id          UUID REFERENCES users(id),
    make_id          INT REFERENCES car_makes(id),
    model_id         INT REFERENCES car_models(id),
    year             INT NOT NULL,
    color_id         INT REFERENCES colors(id),
    wilaya_id        INT REFERENCES wilayas(id),
    commune_id       INT REFERENCES communes(id),
    fuel             VARCHAR(20) CHECK (fuel IN ('essence','diesel','gpl','hybrid','electric')),
    transmission     VARCHAR(20) CHECK (transmission IN ('manual', 'automatic')),
    mileage          INT NOT NULL,
    papers           VARCHAR(20) CHECK (papers IN ('carte_grise','licence','carte_jaune')),
    bodywork         VARCHAR(20) CHECK (bodywork IN ('original','partial_repaint','full_repaint','accident')),
    description      TEXT,
    price            BIGINT NOT NULL,
    price_type       VARCHAR(20) CHECK (price_type IN ('fixed', 'negotiable', 'offered')),
    accepts_exchange BOOLEAN DEFAULT FALSE,
    status           VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'sold', 'deleted')),
    created_at       TIMESTAMP DEFAULT NOW(),
    updated_at       TIMESTAMP
);

-- Listing images table
CREATE TABLE listing_images (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    listing_id  UUID REFERENCES listings(id) ON DELETE CASCADE,
    url         VARCHAR(500) NOT NULL,
    sort_order  INT DEFAULT 0,
    created_at  TIMESTAMP DEFAULT NOW()
);

-- OTP requests table
CREATE TABLE otp_requests (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id     UUID REFERENCES users(id),
    phone       VARCHAR(20) NOT NULL,
    code        VARCHAR(6) NOT NULL,
    channel     VARCHAR(20) CHECK (channel IN ('whatsapp', 'sms')),
    attempts    INT DEFAULT 0,
    expires_at  TIMESTAMP NOT NULL,
    verified_at TIMESTAMP,
    created_at  TIMESTAMP DEFAULT NOW()
);

-- Reference tables (wilayas, communes, car_makes, car_models, colors)
-- Each should have: id, code, fr_name, ar_name
```

### 3.4 Image Processing Pipeline

| Step | Details |
|------|---------|
| **Upload** | Client uploads to `/uploads/image` endpoint |
| **Storage** | Store original in VPS file storage (or S3-compatible) |
| **Processing** | Backend resizes to 4:3 aspect ratio, compresses to ~200KB |
| **Output** | Return CDN/storage URL for processed image |
| **Cleanup** | Delete original after processing |

### 3.5 External Integrations

| Service | Purpose | Provider |
|---------|---------|----------|
| **WhatsApp Business API** | OTP delivery (primary) | Meta / BSP partner |
| **SMS Gateway** | OTP fallback | Twilio or Vonage |
| **Google OAuth** | Social login | Google Identity Services |
| **Apple OAuth** | Social login (iOS) | Sign in with Apple |

---

## 4. Screen Inventory (Phase 1)

| Screen | Purpose | Key Components |
|--------|---------|----------------|
| **Splash** | App loading, session restore | Logo, loading indicator |
| **Language Selection** | First-launch language choice | FR/AR buttons |
| **Home** | Listing feed | Search trigger, listing grid |
| **Search/Filters** | Configure search | Filter inputs, "Show X Results" button |
| **Search Results** | Display filtered listings | Listing cards, sort dropdown |
| **Listing Details** | View single ad | Photo carousel, specs table, Call/WhatsApp buttons, seller info |
| **Auth Gate** | Login prompt | Google/Apple buttons, "Continue as Guest" |
| **Create Account** | Profile setup | Name inputs, Wilaya/Commune pickers |
| **Phone Verification** | OTP entry | Phone input, OTP input, resend button |
| **Create Listing** | Multi-step form | Vehicle details, photo upload, pricing |
| **Profile** | Account overview | Name, status badge, My Listings link, Settings |
| **My Listings** | Seller's own ads | Listing cards (no edit/delete in Phase 1) |
| **Settings** | Preferences | Language toggle, logout |

---

## 5. Acceptance Criteria Checklist

### Core Flow
- [ ] Guest can browse listings without login
- [ ] Guest can view full listing details including phone number
- [ ] Guest can tap Call → phone app opens with number
- [ ] Guest can tap WhatsApp → WhatsApp opens with pre-filled message
- [ ] Search filters work correctly (all 10 filters)
- [ ] Sort options work correctly (5 options)
- [ ] Empty state shown when no results match

### Authentication
- [ ] Google Sign-In works on iOS and Android
- [ ] Apple Sign-In works on iOS
- [ ] Account created with Unverified status
- [ ] User profile saved with name and location

### Phone Verification
- [ ] OTP sent via WhatsApp when user publishes first ad
- [ ] SMS fallback works when WhatsApp fails
- [ ] OTP expires after 5 minutes
- [ ] 3 failed attempts triggers cooldown
- [ ] Successful verification updates user status to Verified

### Listing Creation
- [ ] All required fields enforced
- [ ] Photo upload works (camera + gallery)
- [ ] Publish button disabled until 5+ photos
- [ ] Listing appears in feed immediately after publish

### Localization
- [ ] French renders correctly (LTR)
- [ ] Arabic renders correctly (RTL)
- [ ] Language selection on first launch
- [ ] Language toggle in settings works
- [ ] All static strings localized
- [ ] Reference data (makes, wilayas) in both languages

---

## 6. Technical Constraints

| Constraint | Requirement |
|------------|-------------|
| **Performance** | Listing feed loads in <1.5s on 4G |
| **Image Size** | Compressed images <200KB each |
| **Offline** | Publish button disabled when offline |
| **Debounce** | Prevent double-submit on forms |
| **Phone Format** | Validate +213 format before OTP request |

---

## 7. Out of Scope (Phase 1)

| Feature | Deferred To |
|---------|-------------|
| Pro Dealer accounts | Phase 2 |
| Admin Panel (Flutter Web) | Phase 2 |
| Reporting system | Phase 2 |
| Favorites | Phase 2 |
| Edit listings | Phase 2 |
| Delete listings | Phase 2 |
| Mark as Sold | Phase 2 |
| Hero slider | Phase 3 |
| Similar Ads | Phase 3 |
| View count tracking | Phase 4 |
| Analytics events | Phase 4 |

---

## 8. Dependencies & Prerequisites

Before development begins:

- [ ] **WhatsApp Business API** account approved and configured
- [ ] **SMS provider** (Twilio/Vonage) account set up
- [ ] **Google Cloud Console** project with OAuth configured
- [ ] **Apple Developer** account with Sign in with Apple enabled
- [ ] **Algerian Wilaya/Commune database** (58 Wilayas, ~1,541 Communes) sourced
- [ ] **Car makes/models database** with French and Arabic names
- [ ] **Color palette** defined with French and Arabic names
- [ ] **VPS** provisioned with PostgreSQL installed
- [ ] **Design files** (Figma) for all screens ready

---

## 9. Estimated Effort

| Component | Estimate |
|-----------|----------|
| Backend API (Ktor) | 2-3 weeks |
| Flutter App (all screens) | 2-3 weeks |
| Localization (FR + AR/RTL) | 1 week |
| Integration & Testing | 1 week |
| **Total** | **5-7 weeks** |
