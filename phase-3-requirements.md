# Phase 3: Sub-Requirement Document
## UX Polish & Engagement Features

**Version:** 1.0  
**Created:** 2025-12-12  
**Status:** Approved  
**Parent PRD:** [sayarat-prd.md](./sayarat-prd.md)  
**Prerequisite:** Phase 2 Complete

---

## Definition of Done

> The app feels polished and launch-ready: the homepage features a promotional hero slider, listing details show similar cars, users can share listings via native share sheet with rich previews, and all screens have proper loading/empty/error states.

---

## 1. User Stories

| ID | User Story | Priority | Acceptance Criteria |
|----|------------|----------|---------------------|
| **P3-US01** | As a **user**, I want to **see a hero slider on the homepage**, so I can **discover featured content and announcements**. | 🔴 P0 | 1. Rotating carousel at top of Home screen.<br>2. Auto-advances every 5 seconds.<br>3. Manual swipe navigation.<br>4. Tappable slides link to listings or external URLs.<br>5. Admin can manage slides (Phase 2 Admin Panel extension). |
| **P3-US02** | As a **buyer**, I want to **see similar cars on a listing page**, so I can **explore alternatives if this one isn't perfect**. | 🔴 P0 | 1. "Similar Cars" section at bottom of Listing Details.<br>2. Shows 4 listings with same Make.<br>3. Excludes current listing.<br>4. Tapping navigates to that listing.<br>5. Empty state if no similar cars exist. |
| **P3-US03** | As a **user**, I want to **share a listing**, so I can **send it to friends or post on social media**. | 🔴 P0 | 1. Share button on Listing Details screen.<br>2. Opens native OS share sheet.<br>3. Shared content includes: title, price, main photo, deep link.<br>4. Deep link opens app (if installed) or web fallback. |
| **P3-US04** | As a **user**, I want to **see a rich preview when a listing link is shared**, so **recipients know what they're clicking**. | 🟡 P1 | 1. Open Graph meta tags on listing URLs.<br>2. Preview shows: title (Year Make Model), price, main photo.<br>3. Works on WhatsApp, Facebook, Twitter, iMessage. |
| **P3-US05** | As a **user**, I want to **see skeleton loaders while content loads**, so I **know the app is working**. | 🔴 P0 | 1. Skeleton cards on listing feed during load.<br>2. Skeleton on listing details during load.<br>3. Smooth transition to real content. |
| **P3-US06** | As a **user**, I want to **see helpful empty states**, so I **understand why there's no content and what to do next**. | 🔴 P0 | 1. Search results: "No cars match your filters. Try adjusting your search."<br>2. Favorites: "No favorites yet. Tap the heart on any listing to save it."<br>3. My Listings: "You haven't posted any cars yet." + CTA button. |
| **P3-US07** | As a **user**, I want to **see clear error messages**, so I **know what went wrong and how to recover**. | 🔴 P0 | 1. Network error: "No internet connection. Please check your network."<br>2. Server error: "Something went wrong. Please try again."<br>3. Retry button where applicable. |

---

## 2. Functional Requirements

### 2.1 Hero Slider

| Requirement | Details |
|-------------|---------|
| **Location** | Top of Home screen, above search trigger |
| **Slide Types** | Featured Listing, Announcement, External Link |
| **Content per Slide** | Image (16:9), optional title overlay, tap action |
| **Behavior** | Auto-rotate every 5 seconds, pause on touch, manual swipe |
| **Indicators** | Dot indicators showing current slide |
| **Admin Control** | Manage slides via Admin Panel (add/edit/delete/reorder) |
| **Fallback** | If no slides configured, section hidden (not empty space) |

#### Slide Data Model

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | UUID | ✅ | Unique identifier |
| `image_url` | String | ✅ | 16:9 image URL |
| `title_fr` | String | ❌ | Overlay text (French) |
| `title_ar` | String | ❌ | Overlay text (Arabic) |
| `action_type` | Enum | ✅ | `listing`, `url`, `none` |
| `action_target` | String | ❌ | Listing ID or external URL |
| `sort_order` | Int | ✅ | Display order |
| `is_active` | Boolean | ✅ | Show/hide toggle |
| `start_date` | Date | ❌ | Scheduled start (optional) |
| `end_date` | Date | ❌ | Scheduled end (optional) |

### 2.2 Similar Ads

| Requirement | Details |
|-------------|---------|
| **Location** | Bottom of Listing Details, before Seller Profile |
| **Logic** | Same `make_id` as current listing |
| **Exclusion** | Current listing excluded from results |
| **Limit** | Max 4 listings |
| **Sort** | Newest first |
| **Status Filter** | Only `ACTIVE` listings |
| **Display** | Horizontal scrollable row of listing cards |
| **Empty State** | Section hidden if no similar cars |

### 2.3 Sharing

| Requirement | Details |
|-------------|---------|
| **Button Location** | Listing Details action bar (alongside Call, WhatsApp, Favorite) |
| **Share Mechanism** | Native OS share sheet (Flutter `share_plus` package) |
| **Shared Content** | Pre-filled text + deep link URL |
| **Text Template** | `"Check out this [Year] [Make] [Model] for [Price] on Sayarat: [URL]"` |
| **Deep Link Format** | `https://sayarat.dz/listing/{id}` |
| **App Not Installed** | Falls back to web listing page |

#### Deep Linking

| Platform | Implementation |
|----------|----------------|
| **Android** | App Links (verified domain) |
| **iOS** | Universal Links (AASA file) |
| **Web Fallback** | Simple listing page (can be Phase 4 or static) |

### 2.4 Open Graph Meta Tags

| Tag | Value |
|-----|-------|
| `og:type` | `website` |
| `og:title` | `[Year] [Make] [Model] - [Price]` |
| `og:description` | First 150 chars of description |
| `og:image` | Primary listing photo URL |
| `og:url` | `https://sayarat.dz/listing/{id}` |
| `twitter:card` | `summary_large_image` |

**Implementation:** Server-side rendered meta tags on `/listing/{id}` web route.

### 2.5 Loading States (Skeletons)

| Screen | Skeleton Behavior |
|--------|-------------------|
| **Home Feed** | Grid of skeleton cards (image placeholder + 2 text lines) |
| **Search Results** | Same as Home Feed |
| **Listing Details** | Skeleton for photo carousel, specs table, description |
| **Favorites** | Grid of skeleton cards |
| **My Listings** | List of skeleton cards |

**Design:** Gray shimmer effect, matching card dimensions.

### 2.6 Empty States

| Screen | Message (FR) | Message (AR) | CTA |
|--------|--------------|--------------|-----|
| **Search Results** | "Aucune voiture ne correspond à vos critères." | "لا توجد سيارات تطابق معاييرك." | "Modifier les filtres" |
| **Favorites** | "Vous n'avez pas encore de favoris." | "ليس لديك مفضلات بعد." | None |
| **My Listings** | "Vous n'avez pas encore publié de voiture." | "لم تنشر أي سيارة بعد." | "Publier une annonce" |
| **Seller Listings** | "Ce vendeur n'a pas d'annonces actives." | "هذا البائع ليس لديه إعلانات نشطة." | None |

### 2.7 Error States

| Error Type | Message (FR) | Message (AR) | Action |
|------------|--------------|--------------|--------|
| **No Internet** | "Pas de connexion internet." | "لا يوجد اتصال بالإنترنت." | Retry button |
| **Server Error** | "Une erreur s'est produite. Veuillez réessayer." | "حدث خطأ. يرجى المحاولة مرة أخرى." | Retry button |
| **Not Found** | "Cette annonce n'existe plus." | "هذا الإعلان لم يعد موجوداً." | Back button |
| **Timeout** | "La connexion a expiré." | "انتهت مهلة الاتصال." | Retry button |

---

## 3. Technical Specifications

### 3.1 New API Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| **Hero Slider** ||||
| `/slides` | GET | None | Get active slides (respects date range) |
| `/admin/slides` | GET | Admin JWT | List all slides |
| `/admin/slides` | POST | Admin JWT | Create slide |
| `/admin/slides/{id}` | PUT | Admin JWT | Update slide |
| `/admin/slides/{id}` | DELETE | Admin JWT | Delete slide |
| `/admin/slides/reorder` | POST | Admin JWT | Update sort order |
| **Similar Ads** ||||
| `/listings/{id}/similar` | GET | None | Get similar listings (max 4) |
| **Sharing (Web)** ||||
| `/listing/{id}` | GET | None | Web page with OG meta tags (SSR) |

### 3.2 Database Schema Changes

```sql
-- Hero slider table
CREATE TABLE slides (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    image_url       VARCHAR(500) NOT NULL,
    title_fr        VARCHAR(255),
    title_ar        VARCHAR(255),
    action_type     VARCHAR(20) CHECK (action_type IN ('listing', 'url', 'none')),
    action_target   VARCHAR(500),
    sort_order      INT DEFAULT 0,
    is_active       BOOLEAN DEFAULT TRUE,
    start_date      DATE,
    end_date        DATE,
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP
);

-- Index for active slides query
CREATE INDEX idx_slides_active ON slides (is_active, sort_order) 
    WHERE is_active = TRUE;
```

### 3.3 Admin Panel Extensions

| New Screen | Features |
|------------|----------|
| **Slide Management** | List all slides, add new, edit, delete, drag-to-reorder, preview |

### 3.4 Deep Linking Configuration

**Android (`assetlinks.json`):**
```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "dz.sayarat.app",
    "sha256_cert_fingerprints": ["..."]
  }
}]
```

**iOS (`apple-app-site-association`):**
```json
{
  "applinks": {
    "apps": [],
    "details": [{
      "appID": "TEAM_ID.dz.sayarat.app",
      "paths": ["/listing/*"]
    }]
  }
}
```

### 3.5 Web Landing Page (Minimal)

| Requirement | Details |
|-------------|---------|
| **Purpose** | Render OG meta tags for social previews |
| **Route** | `GET /listing/{id}` |
| **Content** | SSR HTML with meta tags + "Open in App" button |
| **Fallback** | If app not installed, show basic listing info + app store links |
| **Tech** | Can be simple Ktor HTML template or static page generator |

---

## 4. Screen Inventory (Phase 3)

### 4.1 Modified Screens

| Screen | Changes |
|--------|---------|
| **Home** | Add Hero Slider component at top |
| **Listing Details** | Add Similar Ads section, add Share button |
| **All Screens** | Implement skeleton loaders |
| **Search Results** | Add empty state |
| **Favorites** | Add empty state |
| **My Listings** | Add empty state |

### 4.2 New Screens

| Screen | Purpose |
|--------|---------|
| **Error Screen** | Full-screen error with retry (for critical failures) |

### 4.3 Admin Panel Extensions

| Screen | Changes |
|--------|---------|
| **Dashboard** | Add "Slides" count |
| **Slide Management** | New section: list, add, edit, delete, reorder slides |

---

## 5. Acceptance Criteria Checklist

### Hero Slider
- [ ] Slider visible at top of Home screen
- [ ] Auto-advances every 5 seconds
- [ ] Manual swipe works
- [ ] Dot indicators show current slide
- [ ] Tap on listing slide opens Listing Details
- [ ] Tap on URL slide opens external browser
- [ ] Slider hidden if no active slides
- [ ] Admin can add/edit/delete slides
- [ ] Admin can reorder slides via drag-drop
- [ ] Slides respect start/end date scheduling

### Similar Ads
- [ ] Section appears on Listing Details
- [ ] Shows max 4 cars with same Make
- [ ] Current listing excluded
- [ ] Tapping card opens that listing
- [ ] Section hidden if no similar cars

### Sharing
- [ ] Share button visible on Listing Details
- [ ] Native share sheet opens
- [ ] Shared text includes title, price, link
- [ ] Deep link opens app if installed
- [ ] Deep link opens web page if app not installed

### Open Graph
- [ ] WhatsApp shows rich preview (image, title, price)
- [ ] Facebook shows rich preview
- [ ] Twitter shows rich preview

### Loading States
- [ ] Home feed shows skeleton during load
- [ ] Search results show skeleton during load
- [ ] Listing details show skeleton during load
- [ ] Smooth transition from skeleton to content

### Empty States
- [ ] Search with no results shows empty state + filter CTA
- [ ] Favorites with no saves shows empty state
- [ ] My Listings with no posts shows empty state + post CTA

### Error States
- [ ] Network error shows message + retry
- [ ] Server error shows message + retry
- [ ] Not found shows message + back button

---

## 6. Technical Constraints

| Constraint | Requirement |
|------------|-------------|
| **Slide Image Size** | 16:9 aspect ratio, max 500KB, optimized for mobile |
| **Similar Ads Query** | Must be fast (<100ms), use index on make_id |
| **Deep Links** | Requires verified domain ownership |
| **OG Tags** | Requires server-side rendering capability |
| **Skeleton Design** | Match exact card dimensions to prevent layout shift |

---

## 7. Out of Scope (Phase 3)

| Feature | Deferred To |
|---------|-------------|
| View count tracking | Phase 4 |
| Analytics events | Phase 4 |
| Featured/promoted listings (paid) | Post-MVP |
| Price drop alerts | Post-MVP |
| Push notifications | Post-MVP |
| Full consumer web app | Post-MVP |

---

## 8. Dependencies & Prerequisites

Before Phase 3 development begins:

- [ ] **Phase 2 complete and deployed**
- [ ] **Domain verified** for deep linking (sayarat.dz)
- [ ] **SSL certificate** configured
- [ ] **App store listings** created (for store links on web fallback)
- [ ] **Design assets** for hero slider images ready

---

## 9. Estimated Effort

| Component | Estimate |
|-----------|----------|
| Hero Slider (mobile + admin) | 3-4 days |
| Similar Ads | 1-2 days |
| Sharing + Deep Links | 2-3 days |
| Open Graph / Web Landing | 1-2 days |
| Loading Skeletons | 2-3 days |
| Empty + Error States | 1-2 days |
| Integration & Testing | 2-3 days |
| **Total** | **2-3 weeks** |

---

## 10. Confirmed Decisions

| Decision | Choice | Notes |
|----------|--------|-------|
| **Hero Slider Content** | Simple image upload | Admin uploads pre-made banner images |
| **Web Fallback Page** | Teaser + app store links | Minimal page with OG tags, "Download App" CTA |
| **Deep Link Domain** | `sayarat.dz` (placeholder) | Will configure when domain is secured |
