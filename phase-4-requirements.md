# Phase 4: Sub-Requirement Document
## Analytics & Growth Features

**Version:** 1.0  
**Created:** 2025-12-12  
**Status:** Approved  
**Parent PRD:** [sayarat-prd.md](./sayarat-prd.md)  
**Prerequisite:** Phase 3 Complete

---

## Definition of Done

> The team has full visibility into platform health through analytics dashboards. Sellers can track the performance of their listings via view counts. The app is optimized for performance with lazy loading and efficient image handling.

---

## 1. User Stories

| ID | User Story | Priority | Acceptance Criteria |
|----|------------|----------|---------------------|
| **P4-US01** | As a **seller**, I want to **see how many times my listing has been viewed**, so I can **gauge interest in my car**. | 🔴 P0 | 1. View count displayed on My Listings screen.<br>2. View count visible on Listing Details (to owner only).<br>3. Count increments on each unique view.<br>4. Count updates in near real-time. |
| **P4-US02** | As a **product owner**, I want to **track key events**, so I can **measure platform health and user behavior**. | 🔴 P0 | 1. Events tracked: signup, listing created, listing viewed, call clicked, search performed.<br>2. Events include relevant properties.<br>3. Data exportable or viewable in analytics tool. |
| **P4-US03** | As an **admin**, I want to **view platform analytics**, so I can **make data-driven decisions**. | 🟡 P1 | 1. Dashboard shows: total users, total listings, daily active users.<br>2. Charts show trends over time.<br>3. Filterable by date range. |
| **P4-US04** | As a **user**, I want the **app to load images efficiently**, so I can **browse quickly even on slow networks**. | 🔴 P0 | 1. Images lazy-loaded as user scrolls.<br>2. Thumbnails used in listing grids.<br>3. Full images loaded only on detail view.<br>4. Progressive loading with blur placeholder. |
| **P4-US05** | As a **user**, I want the **feed to load smoothly**, so I can **scroll without stuttering**. | 🟡 P1 | 1. Infinite scroll pagination.<br>2. Pre-fetch next page before reaching bottom.<br>3. Smooth 60fps scrolling. |

---

## 2. Functional Requirements

### 2.1 View Count Tracking

| Requirement | Details |
|-------------|---------|
| **Trigger** | User opens Listing Details screen |
| **Counting Logic** | One view per user per listing per 24-hour period (deduplicated) |
| **Guest Views** | Counted using device fingerprint or session ID |
| **Visibility** | View count shown to ALL users (public metric) |
| **Public Visibility** | View count shown to all users (social proof) |
| **Real-time** | Update within 5 minutes (eventual consistency acceptable) |

#### View Deduplication Strategy

| Scenario | Behavior |
|----------|----------|
| Same user, same listing, same day | Count once |
| Same user, same listing, next day | Count again |
| Same user, different listings | Count each |
| Guest (no account) | Use session/device ID for deduplication |

### 2.2 Event Tracking

| Event Name | Trigger | Properties |
|------------|---------|------------|
| `signup_completed` | User creates account | `user_id`, `account_type`, `auth_provider`, `wilaya` |
| `phone_verified` | User completes OTP | `user_id`, `channel` (whatsapp/sms) |
| `listing_created` | Listing published | `listing_id`, `user_id`, `user_type`, `make`, `wilaya`, `price` |
| `listing_viewed` | Listing Details opened | `listing_id`, `viewer_id` (nullable), `source` (feed/search/similar/share) |
| `call_clicked` | Call button tapped | `listing_id`, `viewer_id` (nullable), `seller_type` |
| `whatsapp_clicked` | WhatsApp button tapped | `listing_id`, `viewer_id` (nullable), `seller_type` |
| `search_performed` | Search submitted | `filters_used`, `results_count`, `user_id` (nullable) |
| `favorite_added` | Listing favorited | `listing_id`, `user_id` |
| `listing_shared` | Share button tapped | `listing_id`, `user_id` (nullable), `platform` (if detectable) |
| `listing_reported` | Report submitted | `listing_id`, `reporter_id`, `reason` |

### 2.3 Analytics Dashboard (Admin)

| Metric | Description | Visualization |
|--------|-------------|---------------|
| **Total Users** | All registered users | Number + trend |
| **New Users (Daily/Weekly)** | Signups over time | Line chart |
| **Total Listings** | All active listings | Number + trend |
| **New Listings (Daily/Weekly)** | Listings created over time | Line chart |
| **Daily Active Users (DAU)** | Unique users per day | Line chart |
| **Top Makes** | Most listed car brands | Bar chart |
| **Top Wilayas** | Most active locations | Bar chart |
| **Call/WhatsApp Clicks** | Contact actions per day | Line chart |

### 2.4 Performance Optimizations

#### Image Loading

| Optimization | Details |
|--------------|---------|
| **Thumbnails** | Generate 300px width thumbnails for grid views |
| **Full Size** | Load original (compressed) only on details screen |
| **Lazy Loading** | Load images only when entering viewport |
| **Placeholder** | Simple gray placeholder during load |
| **Caching** | Cache images locally with expiration |

#### Feed Pagination

| Requirement | Details |
|-------------|---------|
| **Page Size** | 20 listings per page |
| **Infinite Scroll** | Append pages as user scrolls |
| **Pre-fetch** | Request next page when 5 items from bottom |
| **Pull to Refresh** | Reload first page on pull-down |
| **Cache** | Cache first 2 pages for instant load |

---

## 3. Technical Specifications

### 3.1 New API Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| **View Tracking** ||||
| `/listings/{id}/view` | POST | Optional JWT | Record a view (deduplicated server-side) |
| `/listings/{id}/stats` | GET | JWT (Owner) | Get listing stats (view count) |
| **Events** ||||
| `/events` | POST | Optional JWT | Record analytics event (batch supported) |
| **Analytics (Admin)** ||||
| `/admin/analytics/overview` | GET | Admin JWT | Get summary stats |
| `/admin/analytics/users` | GET | Admin JWT | User metrics over time |
| `/admin/analytics/listings` | GET | Admin JWT | Listing metrics over time |
| `/admin/analytics/engagement` | GET | Admin JWT | Calls, WhatsApp, shares over time |
| **Images** ||||
| `/uploads/image` | POST | JWT | (Modified) Returns both thumbnail + full URLs |

### 3.2 Database Schema Changes

```sql
-- Listing views table (for counting and deduplication)
CREATE TABLE listing_views (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    listing_id      UUID REFERENCES listings(id) ON DELETE CASCADE,
    viewer_id       UUID REFERENCES users(id),  -- NULL for guests
    session_id      VARCHAR(100),                -- For guest deduplication
    viewed_at       TIMESTAMP DEFAULT NOW()
);

-- Indexes for fast querying
CREATE INDEX idx_listing_views_listing ON listing_views(listing_id);
CREATE INDEX idx_listing_views_dedup ON listing_views(listing_id, viewer_id, viewed_at);
CREATE INDEX idx_listing_views_session ON listing_views(listing_id, session_id, viewed_at);

-- Materialized view count (updated periodically or via trigger)
ALTER TABLE listings ADD COLUMN view_count INT DEFAULT 0;

-- Analytics events table
CREATE TABLE analytics_events (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_name      VARCHAR(50) NOT NULL,
    user_id         UUID REFERENCES users(id),
    session_id      VARCHAR(100),
    properties      JSONB,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- Index for querying events
CREATE INDEX idx_events_name_date ON analytics_events(event_name, created_at);
CREATE INDEX idx_events_user ON analytics_events(user_id) WHERE user_id IS NOT NULL;

-- Daily aggregates table (for fast dashboard queries)
CREATE TABLE daily_stats (
    date            DATE PRIMARY KEY,
    new_users       INT DEFAULT 0,
    new_listings    INT DEFAULT 0,
    active_users    INT DEFAULT 0,
    total_views     INT DEFAULT 0,
    call_clicks     INT DEFAULT 0,
    whatsapp_clicks INT DEFAULT 0,
    searches        INT DEFAULT 0,
    updated_at      TIMESTAMP DEFAULT NOW()
);

-- Thumbnail URL column
ALTER TABLE listing_images ADD COLUMN thumbnail_url VARCHAR(500);
```

### 3.3 Image Processing Pipeline Update

| Step | Phase 1-3 | Phase 4 Addition |
|------|-----------|------------------|
| Upload | Store original | Store original |
| Compress | Resize to 4:3, ~200KB | Same |
| Thumbnail | ❌ | Generate 300px width, ~30KB |
| BlurHash | ❌ | ❌ (using simple gray placeholder) |
| Output | Return `url` | Return `url` + `thumbnail_url` |

### 3.4 Analytics Backend Options

| Option | Pros | Cons | Recommendation |
|--------|------|------|----------------|
| **Custom (PostgreSQL)** | Full control, no cost | Requires building dashboards | ✅ MVP |
| **Mixpanel/Amplitude** | Rich features, funnels | Cost at scale, data leaves server | Phase 5+ |
| **PostHog (self-hosted)** | Open source, feature-rich | Requires additional server | Phase 5+ |

**Recommendation:** Start with custom PostgreSQL + simple admin dashboard. Migrate to dedicated analytics tool when scale requires it.

### 3.5 Client-Side Implementation

| Requirement | Implementation |
|--------------|----------------|
| **Event Batching** | Queue events locally, send in batches every 30s or on app background |
| **Offline Events** | Store in local DB, sync when online |
| **Session ID** | Generate UUID on first launch, persist locally |
| **Lazy Loading** | Use `cached_network_image` with `ListView.builder` |

---

## 4. Screen Inventory (Phase 4)

### 4.1 Modified Screens

| Screen | Changes |
|--------|---------|
| **My Listings** | Add view count badge on each listing card |
| **Listing Details** | Add view count (visible to owner only) |
| **Home Feed** | Implement lazy loading + thumbnails |
| **Search Results** | Implement lazy loading + thumbnails |

### 4.2 Admin Panel Extensions

| Screen | Features |
|--------|----------|
| **Analytics Dashboard** | New section with overview stats + charts |

---

## 5. Acceptance Criteria Checklist

### View Count
- [ ] View recorded when user opens listing details
- [ ] Same user viewing same listing twice in 24h = 1 view
- [ ] Guest views tracked via session ID
- [ ] View count visible on My Listings screen
- [ ] View count visible on Listing Details (owner only)
- [ ] Count updates within 5 minutes

### Event Tracking
- [ ] `signup_completed` fires on registration
- [ ] `phone_verified` fires on OTP success
- [ ] `listing_created` fires on publish
- [ ] `listing_viewed` fires on details open
- [ ] `call_clicked` fires on call button tap
- [ ] `whatsapp_clicked` fires on WhatsApp button tap
- [ ] `search_performed` fires on search submit
- [ ] All events include correct properties
- [ ] Events work for guests (with session ID)

### Analytics Dashboard
- [ ] Total users count displayed
- [ ] Total listings count displayed
- [ ] Daily/weekly trends visible as charts
- [ ] Date range filter works
- [ ] Top makes chart displayed
- [ ] Top wilayas chart displayed

### Performance
- [ ] Grid views use thumbnail images
- [ ] Detail view loads full image
- [ ] Placeholder shown during image load
- [ ] Images lazy-loaded on scroll
- [ ] Infinite scroll works smoothly
- [ ] Next page pre-fetched before reaching bottom
- [ ] Pull-to-refresh works

---

## 6. Technical Constraints

| Constraint | Requirement |
|------------|-------------|
| **View Deduplication** | Server-side, within 24-hour window |
| **Event Batch Size** | Max 50 events per batch |
| **Thumbnail Size** | Max 30KB, 300px width |
| **Dashboard Query Speed** | <500ms for overview stats |
| **Daily Stats** | Computed via scheduled job (every hour) |

---

## 7. Out of Scope (Phase 4)

| Feature | Deferred To |
|---------|-------------|
| Advanced analytics (funnels, cohorts) | Post-MVP |
| A/B testing infrastructure | Post-MVP |
| Real-time analytics | Post-MVP |
| Export to CSV/Excel | Post-MVP |
| Push notifications | Post-MVP |
| Seller insights (best time to post, etc.) | Post-MVP |

---

## 8. Dependencies & Prerequisites

Before Phase 4 development begins:

- [ ] **Phase 3 complete and deployed**
- [ ] **Cron job infrastructure** available for daily stats computation
- [ ] **Charting library** selected for admin panel (e.g., fl_chart, syncfusion)

---

## 9. Estimated Effort

| Component | Estimate |
|-----------|----------|
| View count tracking | 2-3 days |
| Event tracking system | 2-3 days |
| Analytics dashboard (admin) | 3-4 days |
| Image thumbnails + lazy loading | 2-3 days |
| Infinite scroll optimization | 1-2 days |
| Integration & Testing | 2-3 days |
| **Total** | **1.5-2 weeks** |

---

## 10. Confirmed Decisions

| Decision | Choice | Notes |
|----------|--------|-------|
| **View Count Visibility** | Public (all users) | Builds social proof, shown on listing cards and details |
| **Analytics Tool** | Custom PostgreSQL | Simplest to implement, migrate to dedicated tool at scale |
| **Image Placeholder** | Simple gray | Faster implementation, no BlurHash generation needed |
