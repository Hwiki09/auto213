# Step 2: High-Level Phasing Strategy

Based on the updated PRD and vertical slicing principles, I propose the following phased roadmap. Each phase delivers a fully functional, shippable, and testable product increment.

## Proposed Roadmap: Phase 1 → Phase 4

### Phase 1: Walking Skeleton (Core Marketplace Loop)
**Definition of Done:** A private user can browse listings, create an account, verify their phone, post a car for sale with photos, and another user can find that car via search and contact the seller.

| Scope | Details |
| :--- | :--- |
| **Auth** | Google Sign-In (all platforms), Apple Sign-In (iOS only), Guest browsing |
| **User Types** | Private users only (no Pro dealers) |
| **Listings** | Create listing (5+ photos), view listing, basic search/filter |
| **Contact** | View phone number, Call button, WhatsApp deep link |
| **Localization** | French only (defer Arabic/RTL to Phase 2) |
| **Admin** | None (founder manually manages via database if needed) |

**Why This Works:** This is the smallest vertical slice that validates the core marketplace hypothesis — can users post and find cars?

### Phase 2: Trust & Moderation Layer
**Definition of Done:** Professional dealers can sign up and get verified, users can report bad listings, and admins can approve dealers, moderate content, and suspend bad actors via a web dashboard.

| Scope | Details |
| :--- | :--- |
| **Pro Dealers** | Business signup, RC upload, pending/approved states |
| **Admin Panel** | Flutter Web — dealer approval, report review, listing approval (post-edit), account suspension |
| **Reporting** | Users can report listings (scam, fake price, etc.) |
| **Listing Lifecycle** | Edit (with re-verification), Mark as Sold, Delete |
| **Favorites** | Save listings to favorites (logged-in users only) |

**Why This Works:** Adds the trust layer needed before scaling. Pro dealers = more inventory. Moderation = platform safety.

### Phase 3: Arabic/RTL & Polish
**Definition of Done:** Arabic-speaking users can fully use the app in their native language with proper RTL layout, and the overall UX is polished for launch readiness.

| Scope | Details |
| :--- | :--- |
| **Localization** | Full Arabic (AR-DZ) support with RTL layout |
| **Language Picker** | First-launch language selection, settings toggle |
| **UX Polish** | Hero slider, skeleton loaders, empty states, error states |
| **Sharing** | Native share with Open Graph preview |
| **Similar Ads** | Show 4 listings of same Make on details page |

**Why This Works:** Arabic is critical for Algerian market. This phase focuses on launch-readiness polish without new core features.

### Phase 4: Analytics & Growth Features
**Definition of Done:** The team has visibility into platform health via analytics, and sellers can track their ad performance.

| Scope | Details |
| :--- | :--- |
| **Analytics** | Event tracking (listing_created, listing_viewed, call_button_clicked, search_performed, signup_completed) |
| **Ad View Count** | Sellers see view count on their listings |
| **Performance** | Image compression optimization, lazy loading |

**Why This Works:** Provides data to make informed decisions post-launch. View counts incentivize sellers.

---

## Deferred to Post-MVP (Future Phases)

| Feature | Rationale |
| :--- | :--- |
| **Motorcycles** | Category expansion after car marketplace proven |
| **Watermarking** | Image protection — nice-to-have |
| **Saved Search Alerts** | Requires push notification infrastructure |
| **Price Drop Alerts** | Requires event-driven architecture |
| **Social Cards for Sharing** | Marketing feature — not core |
| **Seller Ratings** | Requires transaction history / feedback loop |
| **Web App (consumer-facing)** | Admin web comes first; consumer web deferred |
| **In-app Messaging** | WhatsApp deep link is sufficient for MVP |

---

## Phase Summary Table

| Phase | Name | Key Deliverable | Est. Effort |
| :--- | :--- | :--- | :--- |
| **1** | Walking Skeleton | Core post → browse → contact loop | 4-6 weeks |
| **2** | Trust & Moderation | Pro dealers, Admin panel, Reporting | 3-4 weeks |
| **3** | Arabic/RTL & Polish | Bilingual support, UX polish | 2-3 weeks |
| **4** | Analytics & Growth | Event tracking, View counts | 1-2 weeks |

---

## My Critical Observations

*   **Phase 1 excludes Pro Dealers intentionally.** This keeps the walking skeleton lean. Private users are simpler (no RC verification, no admin approval). We can validate the core loop faster.
*   **French-first is a calculated trade-off.** RTL adds ~25% dev time across all screens. By deferring Arabic to Phase 3, we can ship Phase 1 faster. However, if your target early adopters are primarily Arabic speakers, we should reconsider.
*   **Admin Panel in Phase 2, not Phase 1.** In Phase 1, you (the founder) can manually approve/reject via direct database access. This is acceptable for the first dozen dealers. Phase 2 builds the proper tooling.
*   **No analytics in Phase 1.** We're optimizing for speed-to-market. Basic Postgres queries can answer early questions. Proper event tracking comes in Phase 4.