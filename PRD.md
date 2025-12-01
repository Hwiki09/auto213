# Product Requirements Document: Auto213

| Field | Details |
| :--- | :--- |
| **Status** | `Draft` |
| **Owner (PM)** | [Franky] |
| **Tech Lead** | [Ad] |
| **Design Lead** | [Ou] |
| **Target Release** | [Date 1.1.26] |
| **Version** | 1.0 |
| **Last Updated** | 2025-11-25 |

---

## 1. Executive Summary & Vision

### 1.1 Product Vision
To build the best, largest, and most trustworthy app for buying and selling new and used cars and motorcycles in Algeria. Auto213 aims to create a secure environment for both private users and dealerships.

### 1.2 Problem Statement
Currently, there is no trusted, dedicated platform in Algeria for buying and selling cars and motorcycles that serves both private individuals and professional dealerships with proper verification and security measures.

### 1.3 Proposed Solution
A mobile-first marketplace application that connects car and motorcycle sellers (both private individuals and professional dealerships) with buyers in Algeria, featuring verified listings, guided photo uploads, and a secure environment.

### 1.4 Success Metrics (KPIs)
* **Growth:** Total number of new ads posted (Cars & Motorcycles).
* **Acquisition:** Number of new accounts created (Private Users & Car Dealerships).
* **Brand Awareness:** Total followers and engagement on social media platforms.

### 1.5 Project Scope (MVP)

| **In Scope** | **Out of Scope (Phase 2)** |
| :--- | :--- |
| **Vehicles:** Cars (New/Used), Motorcycles. | **Spare Parts:** (Pièces détachées). |
| **Transaction Types:** Selling (Vente), Exchange (Echange/Tabraz). | **Heavy Machinery:** Trucks, Buses. |
| **Listing Types:** "I am selling" & "I am looking for" (Je cherche). | **Rentals:** (Location). |
| **Users:** Private Individuals & Professional Dealerships. | **In-App Payment:** (Cash only for MVP). |

---

## 2. User Roles & Personas

### 2.1 User Types
The platform supports four distinct user roles:

1.  **Guest (Visitor):** Unregistered user browsing the app.
2.  **Private User (Particulier):** Individual selling their own vehicle.
3.  **Professional Dealer (Showroom):** Registered business (Salle d'exposition) selling multiple vehicles.
4.  **Admin:** Platform owner/moderators.

### 2.2 User Permissions & Verification

| Feature | Guest | Private User | Pro Dealer |
| :--- | :--- | :--- | :--- |
| **Search & Filter** | ✅ Yes | ✅ Yes | ✅ Yes |
| **View Photos & Price** | ✅ Yes | ✅ Yes | ✅ Yes |
| **View Seller Phone Number** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Post Ads** | ❌ No | ✅ Yes (Unlimited) | ✅ Yes (Unlimited) |
| **Verification Level** | None | **Level 1:** SMS OTP (Required for posting ads) | **Level 2:** SMS OTP + **RC Upload** |

### 2.3 Detailed Onboarding Flows

* **Guest Experience:**
    * Can view the full list of cars and motorcycles.
    * Can view seller phone numbers directly.
    * Can call sellers or contact them without logging in.

* **Private User (Particulier):**
    * **Initial Signup:** Requires only First Name, Last Name, Wilaya, Commune. Phone number verification is NOT required for browsing or account creation.
    * **Verification Trigger:** When user attempts to post their first ad, they are prompted to provide phone number and complete SMS OTP verification.
    * **Account States:** 
        - **Unverified:** Can browse, view listings, save favorites (no posting allowed)
        - **Verified:** Can post ads (Unlimited)

* **Professional Dealer (Showroom):**
    * **Signup:** Requires First/lastname of business owner, Business Name, Address (Wilaya/Commune), Phone Number.
    * **Verification:** Must upload a photo of the **Commercial Registry (Registre Commerce/RC)**.
    * **Approval:** Account remains in "Pending" state until Admin manually approves the RC and activates the "Pro" status.

### 2.4 User Stories

| ID | User Story | Priority | Acceptance Criteria |
| :--- | :--- | :--- | :--- |
| **US-01** | As a **guest**, I want to **browse car listings**, so I can **see what's available before signing up**. | 🔴 P0 | 1. Guest can view all listings.<br>2. Guest can view seller phone numbers.<br>3. Guest can contact sellers directly without logging in. |
| **US-02** | As a **private user**, I want to **post a car for sale**, so I can **find a buyer for my vehicle**. | 🔴 P0 | 1. User can post unlimited active listings.<br>2. Minimum 5 photos required.<br>3. Price is mandatory.<br>4. **Phone number verification required before posting first ad.** |
| **US-03** | As a **professional dealer**, I want to **post unlimited listings**, so I can **showcase my entire inventory**. | 🔴 P0 | 1. Pro status verified via RC upload.<br>2. No listing limit.<br>3. Pro badge displayed on listings. |
| **US-04** | As a **buyer**, I want to **filter cars by make, model, and price**, so I can **find exactly what I'm looking for**. | 🔴 P0 | 1. Filters work correctly.<br>2. Results update in real-time.<br>3. Zero results message shown when no matches. |
| **US-05** | As a **user**, I want to **save listings to favorites**, so I can **compare them later**. | 🟡 P1 | 1. Heart icon adds to favorites.<br>2. Favorites accessible from profile.<br>3. Favorites persist across sessions. |
| **US-06** | As an **unverified private user**, I want to **browse listings without phone verification**, so I can **explore the platform before committing**. | 🔴 P0 | 1. User can create account with just name and location.<br>2. User can browse all listings.<br>3. User can save favorites.<br>4. User can view seller phone numbers.<br>5. **User is prompted for phone verification only when attempting to post an ad.** |

---

## 3. Scope & Constraints
### 3.1 In Scope (MVP)
* **Vehicles:** Cars (New/Used), Motorcycles.
* **Transaction Types:** Selling (Vente), Exchange (Echange/Tabraz).
* **Listing Types:** "I am selling" & "I am looking for" (Je cherche).
* **Users:** Private Individuals & Professional Dealerships.
* User registration with SMS OTP verification (required for posting ads)
* Professional dealer verification via RC upload
* Guided photo upload with minimum 5 photos.
* Search and filter functionality.
* Favorites/saved listings.

### 3.2 Out of Scope (Post-MVP)
* **Spare Parts:** (Pièces détachées) - Planned for Phase 2.
* **Heavy Machinery:** Trucks, Buses.
* **Rentals:** (Location).
* **In-App Payment:** Cash only for MVP.
* Mobile App integration (iOS/Android native).
* AI photo enhancement.
* In-app messaging system.

### 3.3 Dependencies
* [ ] SMS Gateway provider for OTP verification (+213 numbers).
* [ ] Image processing service for watermarking and compression.
* [ ] Algerian Wilaya/Commune database.
* [ ] Car makes and models database.

---

## 4. Functional Requirements

### 4.1 Feature Set: Authentication & Onboarding

**4.1.1 Login & Sign Up**
* **Sign In Options:**
    * **Social Login:** Users can sign in using **Google** or **Apple**.
    * **Skip Sign In:** Users can choose to "Skip" the login process and browse as a Guest.

**4.1.2 Onboarding Flow**
* **Account Type Selection:**
    * If the user chooses to create an account, they must select their account type:
        1.  **Personal Account (Particulier):** For individual sellers.
        2.  **Business Account (Professionnel):** For car dealerships and showrooms.
* **Location Details:**
    * Users are required to provide their **Wilaya** and **Commune** during the onboarding process.

### 4.2 Feature Set: Create Listing (Selling Flow)

**4.2.1 General Logic**
* **User Access:** Only logged-in users (Private & Pro) can access the "Sell" button.
* **Private User Verification:** Unverified private users can browse the sell flow but will be prompted to complete phone verification before publishing their first ad.
* **Listing Limit:**
    * Private: Unlimited active listings (after verification).
    * Pro: Unlimited.
* **Category Selection:** User must choose between **Car (Voiture)** or **Motorcycle (Moto)** immediately.

**4.2.2 Vehicle Details - Common Fields (Required)**
* **Brand (Marque):** Dropdown.
* **Model (Modèle):** Dropdown (Dependent on Brand).
* **Year (Année):** Dropdown (1980–Current).
* **Color:** Dropdown.
* **Location:** Select **Wilaya** → Select **Commune**.
* **Description:** Free text area for additional details.
* **Transaction Type:**
    * Selling (Vente).
    * Exchange (Accepte l'échange/Tabraz) - *Toggle Switch*.

**4.2.3 Car Specifics**
* **Fuel (Energie):** Essence, Diesel, GPL (Sirghaz), Hybrid, Electric.
* **Transmission (Boite):** Manuelle, Automatique.
* **Mileage (Km):** Number input.
* **Papers:** Carte Grise, Licence (Moudjahid), Carte Jaune.
* **Bodywork (Sbigha):** Input to declare the paint/accident condition of the vehicle.

**4.2.4 Motorcycle Specifics**
* *Note: Specific technical filters (Cylinder, Type) are TBD. For MVP, use standard Description and Model fields.*

**4.2.5 Pricing & Negotiation (Mandatory)**
* **Validation:** The price field is **Mandatory**. Listings without a price cannot be published. "Price on Request" is **disabled**.
* **Price Types:**
    1.  **Fixed Price:** The seller wants exactly this amount.
    2.  **Negotiable:** The seller sets a starting price but is open to negotiation.
    3.  **Offered (Atawli):** The seller enters the highest offer received so far (e.g., "Atawli 120M").

**4.2.6 Media Upload & Processing (The "Pro" Standard)**

* **Guided Upload Flow (Mandatory):**
    * **Source:** Users can upload photos from their **Gallery** or take new ones via **Camera**.
    * The user cannot just upload random photos. They must follow a step-by-step wizard.
    * **Minimum Photos:** 5 required.
    * **Sequence:**
        1.  **Front View (Avant):** App shows a silhouette overlay to guide framing.
        2.  **Rear View (Arrière):** App prompts for the back.
        3.  **Right Side (Coté Droit)**.
        4.  **Left Side (Coté Gauche)**.
        5.  **Interior/Dashboard (Intérieur)**.
        6.  *Optional:* Engine, Trunk, Odometer.

* **Image Validation:**
    * If the user tries to upload fewer than 5 photos, the "Post Ad" button remains disabled. ???

* **Post-Processing (Backend):**
    * **Watermarking:** (Not in MVP) The system automatically overlays the **Auto213** logo (semi-transparent) on the center or corner of every image to prevent theft by other pages. *Note: This feature is deferred to Phase 2.*
    * **Standardization:**
        * **Dimensions:** All images are resized to a standard aspect ratio (e.g., 4:3) to ensure a clean grid on the home feed.
        * **Compression:** Images are compressed to reduce load times for Algerian 4G networks.
        * **AI:** Enhancing the photos ???

**4.2.7 Draft Listings**
* **Save as Draft:** Users can save their listing progress at any stage before publishing.
* **Resume:** Users can access their drafts from their profile/dashboard and resume editing where they left off.
* **Auto-Save:** (Optional) The system periodically auto-saves the listing progress.

**Corner Cases / Edge Cases:**
* **Network Error:** If API fails, show "Please try again later" (Error code 500).
* **Offline:** If user is offline, disable the submit button.
* **Duplicate Action:** If user clicks button twice rapidly, prevent second request (debounce).


---

### 4.3 Feature Set: Buying & Discovery (Search Logic)

**4.3.1 The Homepage Experience**

* **A. Hero Slider (Top):**
    * A rotating carousel at the very top.
    * **Content:** Promotes "Featured Cars" (Paid ads in V2), "Verified Dealerships," or Admin Announcements (e.g., "Beware of scams").

* **B. The Search Trigger:**
    * Located immediately below the slider.
    * **Visual:** Looks like a Search Bar but acts as a **Button**.
    * **Action:** Tapping it **navigates** the user to the full "Detailed Search Page."

**4.3.2 Detailed Search Page (The Filters)**

* **Primary Filters (Top of Page - In Order):**
    1.  **Make (Marque):** Dropdown (e.g., Renault).
    2.  **Model (Modèle):** Dropdown (Enabled only after Make is selected).
    3.  **Year (Année):** Range selection (Min Year - Max Year).
    4.  **Mileage (Kilométrage):** "Maximum Mileage" input (e.g., < 150,000 km).

* **Secondary Filters (Below):**
    * **Location:** Wilaya → Commune.
    * **Price:** Min - Max.
    * **Fuel:** Diesel/Essence/GPL.
    * **Gearbox:** Manual/Auto.

* **Action:** "Show X Results" button at the bottom (sticky).
* **Zero Results:** If no cars match, show "No results found".

**4.3.3 Sorting Options**
* **Default Sort:** **Newest First** (Date Descending).
* **Manual Sort Options:** The user can change the order to:
    * Price: Low to High (Les moins chers).
    * Price: High to Low.
    * Year: Newest to Oldest.
    * Mileage: Low to High.

**4.3.4 Ad Details Screen (Product Page)**

* **A. Media Gallery (Top):**
    * Swipeable carousel of high-res photos (watermarked).
    * "Full Screen" mode on tap.

* **B. Header Info:**
    * **Price:** Large, bold text (e.g., "320 Millions").
    * **Title:** Year Make Model (e.g., "2022 Renault Clio 5").
    * **Badges:** [Verified Seller], [1st Hand], [Pro].

* **C. Action Bar (Sticky/Floating):**
    * **Call Button:** Reveals phone number (if logged in) → Copies to dialer.
    * **Message Button:** Opens WhatsApp or SMS.
    * **Save:** Heart icon to add to Favorites.

* **D. Technical Data (The Specs):**
    * Displayed in a clean **Table Format**:
        * *Engine:* 1.5 dCi
        * *Fuel:* Diesel
        * *Mileage:* 120,000 km
        * *Gearbox:* Manual
        * *Paint State:* [Diagram or Text]

* **E. Description:**
    * Free text area written by the seller.

* **F. Seller Profile Card (Bottom):**
    * **Photo/Logo:** Avatar of the user or Dealership Logo.
    * **Name:** "Yacine B." or "Auto Blida Showroom".
    * **Rating:** Star Rating (e.g., ⭐ 4.8/5) based on previous buyers (Future feature or Manual rating).
    * **Member Since:** Date joined.
    * **Link:** "View all ads from this seller."

### 4.4 Feature Set: Ad Interactions

**4.4.1 Favorite Ads**
* **Action:** Users can mark any listing as a "Favorite" by tapping the heart icon.
* **State:** The icon toggles between empty (not favorite) and filled (favorite).
* **Persistence:**
    * **Logged-in Users:** Favorites are synced to the backend and persist across devices.
    * **Guest Users:** Favorites are stored locally on the device (Local Storage).
* **Access:** A dedicated "Favorites" page/tab allows users to view all their saved listings.

**4.4.2 Share Ads**
* **Action:** Users can share a listing via a "Share" button on the Ad Details page.
* **Mechanism:** Uses the native OS share sheet (Mobile) or copies link/opens modal (Web).
* **Shared Content:**
    * **Link:** Direct URL to the ad.
    * **Preview:** Should include Ad Title, Price, and Main Photo (Open Graph tags required).
    * **Pre-filled Text:** e.g., "Check out this [Year] [Make] [Model] on Auto213: [Link]"

**4.4.3 Report Ads**
* **Action:** Users can report a problem with an ad.
* **Options:** Users will be shown a list of options to specify what is wrong:
    * Misleading
    * Bad photos
    * Phone number doesn't work
    * Other

**4.4.4 Ad View Tracking**
* **Action:** The system tracks the number of views on each ad.
* **Visibility:** Ad publishers can see the view count for their ads.
* **Purpose:** To allow ad publishers to track the performance of their ads.


### 4.5 Feature Set: Localization
* **Bilingual Support:** The application must provide full bilingual support for **French** and **Arabic**.
* **Language Selection:** Users should be able to select their preferred language during onboarding and change it later in settings.

---

## 5. User Experience (UX/UI)
> **Master Design File:** [Link to Figma/Sketch/AdobeXD]

### 5.1 Screen Flows

**Browsing Flow:**
1.  Home Screen → View Hero Slider
2.  Tap Search Bar → Navigate to Detailed Search Page
3.  Apply Filters → View Results Grid
4.  Tap Listing → View Ad Details Screen
5.  Tap "Call" → View Phone Number (Available to all users including guests)

**Selling Flow:**
1.  Home Screen → Tap "Sell" Button
2.  (If not logged in) → Login/Signup Prompt
3.  Select Category (Car/Motorcycle)
4.  Enter Vehicle Details
5.  Guided Photo Upload (5 minimum)
6.  Set Price & Negotiation Type
7.  Review & Publish

**Onboarding Flow (Private User):**
1.  Tap "Sign Up" → Enter Name, Wilaya, Commune
2.  Account Active (Unverified State)
3.  User can browse listings, save favorites
4.  (When attempting to post ad) → Phone Number Entry + SMS OTP
5.  Account Verified → Can post ads

**Onboarding Flow (Professional Dealer):**
1.  Tap "Sign Up as Pro"
2.  Enter Business Details
3.  Upload RC Photo
4.  Account in "Pending" State
5.  Admin Approval → Pro Status Activated

### 5.2 UI States
* **Ideal State:** Fully populated listing grid with photos, prices, and seller badges.
* **Empty State:** "No results found. Try adjusting your filters." with illustration.
* **Loading State:** Skeleton loaders for listing cards and photo gallery.
* **Error State:** Toast message: "Something went wrong. Please try again."

### 5.3 Copy & Localization
* **Key Messaging:** "Find your next car on Auto213 – The trusted marketplace for Algeria."
* **Supported Languages:** French (FR), Arabic (AR-DZ).

---

## 6. Technical Requirements (Non-Functional)
### 6.1 System Performance
* Page load must be under 1.5 seconds on Algerian 4G networks.
* Search results must return within 200ms.
* Image compression optimized for low-bandwidth connections.

### 6.2 Security & Compliance
* **Auth:** SMS OTP verification required for posting ads (+213 phone numbers). Phone verification NOT required for browsing or account creation.
* **Permissions:** Only `Admin` role can approve Pro dealer accounts.
* **Data:** User phone numbers visible to all users including guests.
* **Image Protection:** Auto213 watermark on all uploaded images (Deferred to Phase 2).

### 6.3 Platform Support
* **Web:** Chrome, Safari, Firefox, Edge (Last 2 versions).
* **Mobile:** Responsive web design (Native apps in Phase 2).

---

## 7. Analytics & Data
*How do we measure success?*

### 7.1 Key Performance Indicators (KPIs)
* **Growth:** Total number of new ads posted (Cars & Motorcycles).
* **Acquisition:** Number of new accounts created (Private Users & Car Dealerships).
* **Brand Awareness:** Total followers and engagement on social media platforms.
* **Engagement:** Average time spent on listing pages.
* **Conversion:** Number of "Call" button clicks per listing view.

### 7.2 Tracking Events
| Event Name | Trigger | Properties to Capture |
| :--- | :--- | :--- |
| `listing_created` | User publishes a new listing | `category` (car/moto), `user_type` (private/pro), `wilaya` |
| `listing_viewed` | User views ad details page | `listing_id`, `category`, `source` (search/homepage) |
| `call_button_clicked` | User clicks "Call" on listing | `listing_id`, `seller_type` |
| `search_performed` | User applies filters and searches | `filters_used`, `results_count` |
| `signup_completed` | User completes registration | `user_type`, `verification_method` |
| `pro_verification_submitted` | Dealer uploads RC document | `dealer_id`, `timestamp` |

---

## 8. Go-to-Market (GTM) Strategy
* **Marketing:** Blog post, Email blast to active users.
* **Customer Support:** FAQs updated, Support team training session scheduled for [Date].
* **Internal:** Demo at All-Hands meeting.

---

## 9. Risks & Open Questions
### 9.1 Risks
* **Risk:** SMS gateway reliability for OTP delivery in Algeria.
    * **Mitigation:** Partner with reliable local SMS provider; implement retry logic.
* **Risk:** Image theft by competitors despite watermarking.
    * **Mitigation:** Place watermark strategically; consider visible + invisible watermarks.
* **Risk:** Fraudulent listings or scam sellers.
    * **Mitigation:** Implement reporting system; manual review process; verified seller badges.
* **Risk:** Low adoption by professional dealerships.
    * **Mitigation:** Offer free Pro accounts during launch period; direct outreach to showrooms.

### 9.2 Open Questions (Q&A)
| Question | Assignee | Answer |
| :--- | :--- | :--- |
| What additional info is needed for Private User signup? | @PM | **RESOLVED:** Only Name, Wilaya, Commune required. Phone verification deferred until ad posting. |
| Should Color field be a dropdown with predefined options? | @Design | *Pending* |
| Is Location (Wilaya/Commune) required or optional? | @PM | *Pending* |
| Should image compression be automatic or user-controlled? | @Tech | *Pending* |
| Should "Post Ad" button be disabled if <5 photos? | @PM | *Pending* |
| Will AI photo enhancement be included in MVP? | @Tech | *Pending* |

---

## 10. Appendix
* **Glossary:**
    * **Wilaya:** Administrative division in Algeria (equivalent to province/state).
    * **Commune:** Subdivision within a Wilaya (equivalent to municipality).
    * **RC (Registre Commerce):** Commercial Registry document required for business verification.
    * **OTP:** One-Time Password sent via SMS for verification.
    * **Tabraz/Echange:** Vehicle exchange/trade-in.
    * **Atawli:** "Highest offer received" - a pricing term indicating the seller has received this amount as an offer.
    * **Carte Grise:** Vehicle registration document.
    * **Carte Jaune:** Temporary vehicle registration.
    * **Sirghaz/GPL:** LPG (Liquefied Petroleum Gas) fuel type.
    * **Sbigha:** Paint/bodywork condition of a vehicle.
* **Reference Docs:** Links to previous PRDs or technical specs.
