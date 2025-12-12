# Phase 2: Sub-Requirement Document
## Trust & Moderation Layer

**Version:** 1.0  
**Created:** 2025-12-12  
**Status:** Approved  
**Parent PRD:** [sayarat-prd.md](./sayarat-prd.md)  
**Prerequisite:** Phase 1 Complete

---

## Definition of Done

> Professional dealers can sign up, upload their RC document, and get verified by an admin. Users can save listings to favorites, edit/delete their own listings, and report suspicious ads. Admins can approve dealers, review reports, and suspend bad actors via a Flutter Web dashboard.

---

## 1. User Stories

| ID | User Story | Priority | Acceptance Criteria |
|----|------------|----------|---------------------|
| **P2-US01** | As a **professional dealer**, I want to **create a business account**, so I can **list my showroom's inventory**. | 🔴 P0 | 1. "Pro" signup option available.<br>2. Collect: Owner name, Business name, Wilaya/Commune, Phone.<br>3. Account created in "Pending" state. |
| **P2-US02** | As a **professional dealer**, I want to **upload my RC document**, so I can **get verified as a legitimate business**. | 🔴 P0 | 1. Photo upload for RC (Registre Commerce).<br>2. Submission confirmation shown.<br>3. Status remains "Pending" until admin approval. |
| **P2-US03** | As a **verified dealer**, I want to **post listings with a Pro badge**, so **buyers know I'm a trusted business**. | 🔴 P0 | 1. Pro badge displayed on all dealer listings.<br>2. Dealer profile shows business name + logo.<br>3. "View all ads from this seller" works. |
| **P2-US04** | As a **logged-in user**, I want to **save listings to favorites**, so I can **compare cars later**. | 🔴 P0 | 1. Heart icon toggles favorite state.<br>2. Favorites synced to backend (persist across devices).<br>3. Dedicated Favorites screen accessible from navigation.<br>4. Guests prompted to log in when tapping favorite. |
| **P2-US05** | As a **seller**, I want to **edit my listing**, so I can **correct mistakes or update details**. | 🔴 P0 | 1. Edit button on My Listings screen.<br>2. All fields editable including photos.<br>3. Edited listing requires re-approval (goes to "Pending Review" state).<br>4. Listing hidden from feed until approved. |
| **P2-US06** | As a **seller**, I want to **mark my listing as sold**, so it **no longer appears in search results**. | 🔴 P0 | 1. "Mark as Sold" button available.<br>2. Listing removed from active feed.<br>3. Listing still visible in seller's "My Listings" with "SOLD" badge.<br>4. Action is reversible (can re-activate). |
| **P2-US07** | As a **seller**, I want to **delete my listing**, so I can **remove it permanently**. | 🟡 P1 | 1. Delete button with confirmation dialog.<br>2. Listing permanently removed.<br>3. Action is irreversible. |
| **P2-US08** | As a **user**, I want to **report a suspicious listing**, so I can **help keep the platform safe**. | 🔴 P0 | 1. "Report" button visible on listing details.<br>2. Report reasons: Scam, Fake Price, Bad Photos, Phone Doesn't Work, Other.<br>3. Optional comment field.<br>4. Confirmation shown after submission. |
| **P2-US09** | As an **admin**, I want to **approve or reject dealer applications**, so I can **verify legitimate businesses**. | 🔴 P0 | 1. View pending dealer list.<br>2. View RC document image.<br>3. Approve → Dealer becomes "Pro Active".<br>4. Reject → Dealer can re-submit new RC. |
| **P2-US10** | As an **admin**, I want to **review reported listings**, so I can **remove bad content**. | 🔴 P0 | 1. View list of reported listings (sorted by report count).<br>2. See report reasons breakdown.<br>3. Actions: Dismiss report, Hide listing, Delete listing. |
| **P2-US11** | As an **admin**, I want to **approve edited listings**, so I can **prevent sellers from adding bad content after initial approval**. | 🔴 P0 | 1. View list of listings pending re-approval.<br>2. See diff of changes (optional P1).<br>3. Approve → Listing goes live.<br>4. Reject → Listing stays hidden + seller notified. |
| **P2-US12** | As an **admin**, I want to **suspend user accounts**, so I can **ban repeat offenders**. | 🟡 P1 | 1. Suspend button on user profile.<br>2. Suspended users cannot log in.<br>3. All their listings hidden from feed.<br>4. Suspension can be reversed. |

---

## 2. Functional Requirements

### 2.1 Professional Dealer Accounts

| Requirement | Details |
|-------------|---------|
| **Account Type** | `PRO_DEALER` (in addition to existing `PRIVATE_USER`) |
| **Required Fields** | Owner first name, Owner last name, Business name, Wilaya, Commune, Phone |
| **RC Document** | Single photo upload (JPEG/PNG, max 5MB) |
| **Account States** | `PENDING` → `APPROVED` or `REJECTED` |
| **Re-submission** | If rejected, dealer sees rejection reason and can upload new RC |
| **Rejection Reasons** | Dropdown: Blurry RC, Expired RC, Name Mismatch, Invalid Document, Other |
| **Pro Badge** | Visual badge on all listings and profile |

### 2.2 Favorites System

| Requirement | Details |
|-------------|---------|
| **Access** | Logged-in users only (Private + Pro) |
| **Storage** | Server-side (synced across devices) |
| **Toggle** | Heart icon on listing card and details page |
| **Limit** | No limit on number of favorites |
| **Persistence** | Favorites persist even if listing marked as sold |
| **Deleted Listings** | Remove from favorites automatically |

### 2.3 Listing Lifecycle

| State | Description | Visibility |
|-------|-------------|------------|
| `ACTIVE` | Live and searchable | ✅ Public |
| `PENDING_REVIEW` | Newly edited, awaiting admin approval | ❌ Hidden |
| `SOLD` | Marked as sold by seller | ❌ Hidden from search, visible in My Listings |
| `DELETED` | Permanently removed | ❌ Gone |
| `SUSPENDED` | Hidden by admin due to report (recoverable) | ❌ Hidden |

| Action | Trigger | Result |
|--------|---------|--------|
| **Edit** | Seller edits any field | Status → `PENDING_REVIEW` |
| **Mark Sold** | Seller taps "Mark Sold" | Status → `SOLD` |
| **Reactivate** | Seller taps "Reactivate" on sold listing | Status → `ACTIVE` |
| **Delete** | Seller confirms deletion | Status → `DELETED` (soft delete) |
| **Suspend** | Admin action | Status → `SUSPENDED` |
| **Unsuspend** | Admin reverses suspension | Status → `ACTIVE` |

### 2.4 Reporting System

| Requirement | Details |
|-------------|---------|
| **Report Reasons** | `SCAM`, `FAKE_PRICE`, `BAD_PHOTOS`, `PHONE_NOT_WORKING`, `OTHER` |
| **Comment** | Optional free text (max 500 chars) |
| **Duplicate Prevention** | One report per user per listing |
| **Visibility** | Reporters are anonymous to seller |
| **Admin View** | Aggregated report count + reason breakdown |

### 2.5 Admin Panel (Flutter Web)

| Section | Features |
|---------|----------|
| **Dashboard** | Summary stats: pending dealers, pending reviews, open reports |
| **Dealer Approvals** | List of pending dealers, view RC, approve/reject |
| **Listing Reviews** | List of edited listings pending approval, approve/reject |
| **Reports** | List of reported listings, view reports, dismiss/hide/delete |
| **User Management** | Search users, view profile, suspend/unsuspend |

---

## 3. Technical Specifications

### 3.1 New API Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| **Dealer Onboarding** ||||
| `/auth/register/dealer` | POST | None | Create dealer account (pending state) |
| `/dealers/me/rc` | POST | JWT | Upload RC document |
| `/dealers/me/rc` | PUT | JWT | Re-upload RC after rejection |
| `/dealers/me` | GET | JWT | Get dealer profile + status |
| **Favorites** ||||
| `/favorites` | GET | JWT | Get user's favorite listings |
| `/favorites/{listingId}` | POST | JWT | Add to favorites |
| `/favorites/{listingId}` | DELETE | JWT | Remove from favorites |
| **Listing Lifecycle** ||||
| `/listings/{id}` | PUT | JWT (Owner) | Update listing (triggers re-review) |
| `/listings/{id}/sold` | POST | JWT (Owner) | Mark as sold |
| `/listings/{id}/reactivate` | POST | JWT (Owner) | Reactivate sold listing |
| `/listings/{id}` | DELETE | JWT (Owner) | Delete listing |
| **Reporting** ||||
| `/listings/{id}/report` | POST | JWT | Submit report |
| **Admin Endpoints** ||||
| `/admin/dealers/pending` | GET | Admin JWT | List pending dealer applications |
| `/admin/dealers/{id}/approve` | POST | Admin JWT | Approve dealer |
| `/admin/dealers/{id}/reject` | POST | Admin JWT | Reject dealer (with reason) |
| `/admin/listings/pending-review` | GET | Admin JWT | List listings pending re-approval |
| `/admin/listings/{id}/approve` | POST | Admin JWT | Approve edited listing |
| `/admin/listings/{id}/reject` | POST | Admin JWT | Reject edited listing |
| `/admin/reports` | GET | Admin JWT | List reported listings |
| `/admin/reports/{id}/dismiss` | POST | Admin JWT | Dismiss report |
| `/admin/listings/{id}/suspend` | POST | Admin JWT | Suspend listing |
| `/admin/listings/{id}/unsuspend` | POST | Admin JWT | Unsuspend listing (restore to active) |
| `/admin/users` | GET | Admin JWT | Search/list users |
| `/admin/users/{id}/suspend` | POST | Admin JWT | Suspend user |
| `/admin/users/{id}/unsuspend` | POST | Admin JWT | Unsuspend user |
| `/admin/stats` | GET | Admin JWT | Dashboard summary stats |

### 3.2 Database Schema Changes

```sql
-- Add account_type to users table
ALTER TABLE users ADD COLUMN account_type VARCHAR(20) DEFAULT 'private' 
    CHECK (account_type IN ('private', 'pro_dealer'));

-- Add suspended status
ALTER TABLE users ALTER COLUMN status TYPE VARCHAR(20);
ALTER TABLE users DROP CONSTRAINT IF EXISTS users_status_check;
ALTER TABLE users ADD CONSTRAINT users_status_check 
    CHECK (status IN ('unverified', 'verified', 'suspended'));

-- Pro Dealer profiles
CREATE TABLE dealer_profiles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID UNIQUE REFERENCES users(id),
    business_name   VARCHAR(255) NOT NULL,
    rc_document_url VARCHAR(500),
    approval_status VARCHAR(20) DEFAULT 'pending' 
                    CHECK (approval_status IN ('pending', 'approved', 'rejected')),
    approved_by     UUID REFERENCES users(id),
    approved_at     TIMESTAMP,
    rejection_reason TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP
);

-- Favorites table
CREATE TABLE favorites (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id     UUID REFERENCES users(id) ON DELETE CASCADE,
    listing_id  UUID REFERENCES listings(id) ON DELETE CASCADE,
    created_at  TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, listing_id)
);

-- Update listings status enum
ALTER TABLE listings DROP CONSTRAINT IF EXISTS listings_status_check;
ALTER TABLE listings ADD CONSTRAINT listings_status_check 
    CHECK (status IN ('active', 'pending_review', 'sold', 'deleted', 'suspended'));

-- Reports table
CREATE TABLE reports (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    listing_id  UUID REFERENCES listings(id) ON DELETE CASCADE,
    reporter_id UUID REFERENCES users(id),
    reason      VARCHAR(30) CHECK (reason IN ('scam', 'fake_price', 'bad_photos', 'phone_not_working', 'other')),
    comment     TEXT,
    status      VARCHAR(20) DEFAULT 'open' CHECK (status IN ('open', 'dismissed', 'actioned')),
    reviewed_by UUID REFERENCES users(id),
    reviewed_at TIMESTAMP,
    created_at  TIMESTAMP DEFAULT NOW(),
    UNIQUE(listing_id, reporter_id)
);

-- Admin users table (simple role-based)
CREATE TABLE admin_roles (
    user_id     UUID PRIMARY KEY REFERENCES users(id),
    role        VARCHAR(20) DEFAULT 'moderator' CHECK (role IN ('moderator', 'super_admin')),
    created_at  TIMESTAMP DEFAULT NOW()
);

-- Listing edit history (optional, for diff view)
CREATE TABLE listing_edits (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    listing_id      UUID REFERENCES listings(id),
    previous_data   JSONB NOT NULL,
    new_data        JSONB NOT NULL,
    reviewed_by     UUID REFERENCES users(id),
    review_status   VARCHAR(20) CHECK (review_status IN ('pending', 'approved', 'rejected')),
    reviewed_at     TIMESTAMP,
    created_at      TIMESTAMP DEFAULT NOW()
);
```

### 3.3 Admin Authentication

| Requirement | Details |
|-------------|---------|
| **Admin Identification** | Separate `admin_roles` table |
| **Initial Admin** | Created manually in database (founder) |
| **Admin JWT** | Same auth system, but with `role` claim |
| **Permission Check** | Middleware validates admin role on `/admin/*` routes |

---

## 4. Screen Inventory (Phase 2)

### 4.1 Mobile App (New/Modified Screens)

| Screen | Type | Purpose |
|--------|------|---------|
| **Account Type Selection** | New | Choose Private vs Pro during signup |
| **Pro Dealer Signup** | New | Business details form |
| **RC Upload** | New | Photo upload for RC document |
| **Pro Pending Status** | New | "Awaiting approval" message |
| **Favorites** | New | List of saved listings |
| **My Listings** | Modified | Add Edit, Mark Sold, Delete actions |
| **Edit Listing** | New | Pre-filled listing form for editing |
| **Listing Details** | Modified | Add Favorite button, Report button |
| **Report Listing** | New | Report reason selection + submit |
| **Seller Profile** | Modified | Show Pro badge, business name for dealers |

### 4.2 Admin Panel (Flutter Web — New)

| Screen | Purpose |
|--------|---------|
| **Login** | Admin authentication |
| **Dashboard** | Summary stats, quick actions |
| **Pending Dealers** | List + detail view + approve/reject |
| **Pending Reviews** | Edited listings awaiting approval |
| **Reports** | Reported listings + actions |
| **User Search** | Find user by name/phone/email |
| **User Detail** | View user info, listings, suspend button |

---

## 5. Acceptance Criteria Checklist

### Pro Dealer Flow
- [ ] Pro signup option available on account type screen
- [ ] Dealer can enter business details
- [ ] Dealer can upload RC photo
- [ ] Dealer sees "Pending" status after submission
- [ ] Dealer cannot post listings while pending
- [ ] Approved dealer can post with Pro badge
- [ ] Rejected dealer can re-upload RC

### Favorites
- [ ] Heart icon visible on listing cards and details
- [ ] Tap toggles favorite state (filled/empty heart)
- [ ] Favorites persisted to server
- [ ] Favorites screen shows all saved listings
- [ ] Guest tapping heart is prompted to log in
- [ ] Removing favorite works
- [ ] Deleted listings auto-removed from favorites

### Listing Lifecycle
- [ ] Edit button visible on My Listings
- [ ] All fields editable including photos
- [ ] Edited listing moves to "Pending Review"
- [ ] Listing hidden from public feed during review
- [ ] Mark Sold removes from search results
- [ ] Mark Sold shows "SOLD" badge in My Listings
- [ ] Reactivate brings listing back to active
- [ ] Delete shows confirmation dialog
- [ ] Delete permanently removes listing

### Reporting
- [ ] Report button visible on listing details
- [ ] Report reasons displayed as selection
- [ ] Optional comment field works
- [ ] Confirmation shown after report
- [ ] Cannot report same listing twice
- [ ] Reporter identity hidden from seller

### Admin Panel
- [ ] Admin can log in via web
- [ ] Dashboard shows pending counts
- [ ] Can view pending dealer list
- [ ] Can view RC document
- [ ] Can approve dealer → status becomes "Approved"
- [ ] Can reject dealer with reason → dealer sees reason
- [ ] Rejected dealer can re-submit new RC
- [ ] Can view listings pending review
- [ ] Can approve edited listing → goes live
- [ ] Can reject edited listing → stays hidden
- [ ] Can view reported listings
- [ ] Can dismiss report
- [ ] Can suspend listing
- [ ] Can unsuspend listing (restore to active)
- [ ] Can search users
- [ ] Can suspend/unsuspend user

---

## 6. Technical Constraints

| Constraint | Requirement |
|------------|-------------|
| **RC Upload** | Max 5MB, JPEG/PNG only |
| **Admin Auth** | Role-based middleware on all `/admin/*` routes |
| **Soft Delete** | Listings marked "deleted" retained for 30 days |
| **Report Limit** | One report per user per listing |
| **Edit Review** | Required for any field change (no exceptions in Phase 2) |

---

## 7. Out of Scope (Phase 2)

| Feature | Deferred To |
|---------|-------------|
| Hero slider | Phase 3 |
| Similar Ads | Phase 3 |
| Sharing functionality | Phase 3 |
| View count tracking | Phase 4 |
| Analytics events | Phase 4 |
| Rejection reason displayed to dealer | Post-MVP |
| Edit diff view in admin | Post-MVP (optional) |
| Push notifications | Post-MVP |

---

## 8. Dependencies & Prerequisites

Before Phase 2 development begins:

- [ ] **Phase 1 complete and deployed**
- [ ] **Admin user created** in database (founder account)
- [ ] **Flutter Web** project initialized (same codebase or separate)
- [ ] **RC document storage** — S3-compatible or VPS file storage configured
- [ ] **Admin subdomain/URL** decided (e.g., admin.sayarat.dz)

---

## 9. Estimated Effort

| Component | Estimate |
|-----------|----------|
| Pro Dealer signup + RC upload | 3-4 days |
| Favorites system | 2-3 days |
| Listing lifecycle (Edit/Sold/Delete) | 3-4 days |
| Reporting system | 2-3 days |
| Admin Panel (Flutter Web) | 5-7 days |
| Backend API additions | 3-4 days |
| Integration & Testing | 3-4 days |
| **Total** | **3-4 weeks** |

---

## 10. Confirmed Decisions

| Decision | Choice | Rationale |
|----------|--------|----------|
| **Edit Re-approval** | Strict — any edit requires admin approval | Prevents bad actors from sneaking in changes |
| **Rejection Reason** | Dropdown shown to dealer | Better UX, dealer knows how to fix |
| **Admin Panel** | Flutter Web | Consistent with mobile codebase |
| **Suspended Listings** | Recoverable by admin | Allows reversing mistakes |
