# Selcom SaaS Store Admin — Angular & PWA Master Architectural Specification

> **Document Version:** 2.0.0 (Exhaustive 62-Module Enterprise Edition)  
> **Target Frontend Framework:** Angular 18+ (Standalone Components, Signals, Reactive Forms, Material/Tailwind)  
> **Source Platform Analyzed:** Selcom Vendor Portal (`https://admin.bdeshio.com/`)  
> **Author:** Antigravity AI Engine (Local DeepMind Agent) for Gemini Hydrogen (Google AI Studio)

---

## 1. Executive Architecture & UI Paradigm

Selcom's store admin panel is engineered as an **App-like Progressive Web Application (PWA)** featuring a responsive dual-view architecture:

```
+---------------------------------------------------------------------------------------------------+
|                                          ANGULAR 18+ SPA                                          |
+---------------------------------------------------------------------------------------------------+
|  DESKTOP VIEWPORT (>= 1024px)                            |  MOBILE PWA VIEWPORT (< 768px)         |
|  - Full Left Sticky Navigation (.side-nav-main)           |  - Compact Header (Logo + Actions)    |
|  - Multi-level Accordion Menus (Categorized sub-routes)   |  - Mobile Bottom Bar (5 Core Tabs)    |
|  - Global Top Bar (Tutorial, Support, Search, User Info)  |  - Full-screen Slide-over Menu Drawer |
|  - Admin Panel Version & Update Box at Sidebar Bottom     |  - Touch-optimized Modals & Sheets    |
|  - High-density Tables (Sorting, Filtering, Pagination)   |  - Card-based Mobile List Views       |
+---------------------------------------------------------------------------------------------------+
```

### Core Angular Architectural Stack:
- **Framework:** Angular 18+ (Standalone Components, `provideRouter`, Lazy Route Chunks)
- **State Management:** Angular Signals (`signal`, `computed`, `effect`) + RxJS for Event Streams
- **UI Component System:** Angular Material (`@angular/material`) combined with Tailwind CSS utility classes
- **Form Architecture:** Angular `ReactiveFormsModule` (`FormBuilder`, typed `FormGroup`, custom async validators)
- **PWA & Offline Integration:** `@angular/service-worker` + Web App Manifest + Push Notifications

---

## 2. Global Layout & Navigation Architecture

### A. Desktop Shell (`app-pages`)
- **Left Navigation (`app-sidebar`):** Width `260px`, dark/light themed, collapsible accordion items (`menu-item-wrapper`).
  - Top: Store Brand Logo (`.brand-container`) + Collapse Toggle.
  - Body: Scrollable navigation menu tree with icon badges.
  - Footer: **Admin Panel Version Widget** (`.sidebar-panel-meta__card`) showing Current Version, Last Update, Latest Version, and `[Update now]` action.
- **Main Viewport (`main.container-main`):** `flex: 1`, padded `24px`, responsive layout for analytical widgets and tables.
- **Top Bar (`app-header`):**
  - Search trigger input (`Search anything...`)
  - Direct Action Buttons: `[▶ Tutorial]` button, `[Support]` button (purple pill with icon).
  - Quick Icons: Language Switcher (Bangla / English / Italian), Notifications bell with count badge, Subscription Badge (`Light Package 282 days left`), Profile Avatar.

### B. Mobile PWA Shell (`app-mobile-nav`)
- **Top Bar:** Store Logo, Support button, Language selector, Package badge.
- **Bottom Navigation Bar (Fixed 64px):**
  1. `Dashboard` (`space_dashboard`) ➔ `/dashboard`
  2. `Product` (`inventory_2`) ➔ `/product/all-product`
  3. `Order List` (`reorder`) ➔ `/order/all-order`
  4. `Notification` (`notifications`) ➔ `/notifications`
  5. `Menu` (`menu`) ➔ Opens full-screen sliding drawer (`app-mobile-menu-drawer`)
- **Mobile Menu Drawer:** Contains the complete accordion tree, Support & Tutorial links, Store Profile, and Logout.

---

## 3. Exhaustive Module Catalog & Specifications (62 Modules)

All visual proof is preserved with 100% fidelity:
- **Desktop Screenshots Folder:** `.ai/selcom-audit/desktop/`
- **Mobile PWA Screenshots Folder:** `.ai/selcom-audit/mobile/`

---

### Module 01: Dashboard & Analytics Overview
- **Route:** `/dashboard`
- **Component:** `DashboardComponent` (`app-dashboard`)
- **Visuals:** [Desktop View](desktop/01_dashboard.png) | [Mobile View](mobile/01_dashboard.png)
- **Key Features:**
  - **8 Real-time KPI Cards:** Today Orders, Today Courier Orders, Confirmed Orders, Pending Orders, Cancelled Orders (with Cancel Rate %), Hold Orders, Delivered Orders, Read Orders.
  - **Date Range Filter:** Custom range picker + quick filters (`Today`, `Yesterday`, `Last 7 Days`, `This Month`).
  - **Interactive Analytics:** Monthly, Weekly, Yearly Sales & Income Chart (`app-dashboard-professional-line-chart`).
  - **Recent Orders Widget:** Quick table with status badges and one-click invoice views.
  - **Top Selling Products Card:** Thumbnail, Title, Sold Quantity, Total Revenue.

---

### Module 02: All Orders Management (Core Engine)
- **Route:** `/order/all-order`
- **Component:** `OrderListComponent`
- **Visuals:** [Desktop View](desktop/02_orders_all.png) | [Mobile View](mobile/02_orders_all.png)
- **Table Data Model:**
  `SL | IMAGE | ORDER ID | NAME | PHONE NO | ORDER FROM | ORDER DATE TIME | PAYMENT TYPE | ORDER STATUS | COURIER | GRAND TOTAL | NOTE | LAST STATUS | ACTIONS`
- **Capabilities:**
  - **Status Filter Tabs:** All, Pending, Confirmed, Shipped, Delivered, Cancelled, Hold, Return.
  - **Multi-Courier Sync:** "Sync Courier Status" button calling live Steadfast/Pathao APIs.
  - **Bulk Export & Summary:** "Download All Orders (CSV)", "Download Products Summary".
  - **Order Actions:** View Invoice, Print Shipping Label, Courier Booking modal, Edit Billing, Status Change.

---

### Module 03: Incomplete Orders (Abandoned Cart Recovery)
- **Route:** `/order/all-incomplete-order`
- **Component:** `IncompleteOrderComponent`
- **Visuals:** [Desktop View](desktop/03_orders_incomplete.png) | [Mobile View](mobile/03_orders_incomplete.png)
- **Table Model:** `SL | IMAGE | ORDER ID | NAME | PHONE NO | ORDER FROM | ORDER DATE TIME | PAYMENT TYPE | GRAND TOTAL | NOTE | ACTIONS`
- **Capabilities:** Tracks prospective buyers who filled out the checkout form but dropped off before completing. Quick WhatsApp and Direct Call action triggers.

---

### Module 04: Product Inventory & Catalog
- **Route:** `/product/all-product`
- **Component:** `ProductListComponent`
- **Visuals:** [Desktop View](desktop/04_products_all.png) | [Mobile View](mobile/04_products_all.png)
- **Table Model:** `SL | IMAGE | NAME | CATEGORY | STOCK | OPENING STOCK | TAGS | STATUS | ACTIONS`
- **Capabilities:** Instant inline stock update, Low-stock visual warning badge, Status toggle (Active / Inactive), Column picker, Bulk delete/export.

---

### Module 05: Product Creation & Form Schema
- **Route:** `/product/add-product`
- **Component:** `AddProductComponent`
- **Visuals:** [Desktop View](desktop/05_product_add.png) | [Mobile View](mobile/05_product_add.png)
- **Reactive Form Schema:**
  - **General:** Product Name, Slug, Category, Sub-Category, Brand, SKU, Model, Unit.
  - **Pricing:** Regular Price, Sale Price, Purchase Cost.
  - **Media:** Desktop Image (700x700), Mobile Image (1200xAny), Size Chart (300x300), Gallery Uploads.
  - **Variants & Inventory:** Size, Color, Variant Matrix (SKU, Price, Stock per combo).
  - **Rich Text & SEO:** WYSIWYG Description, Return Policy, Meta Title, Meta Description, Keywords.

---

### Module 06: Categories Taxonomy
- **Route:** `/catalog/all-category`
- **Component:** `CategoryComponent`
- **Visuals:** [Desktop View](desktop/06_catalog_categories.png) | [Mobile View](mobile/06_catalog_categories.png)
- **Table Model:** `IMAGE | NAME | PRODUCTS COUNT | STATUS | PRIORITY SORT | ACTIONS`

---

### Module 07: Storefront Theme Customizer
- **Route:** `/customization/theme-view`
- **Component:** `ThemeCustomizerComponent`
- **Visuals:** [Desktop View](desktop/07_customization_theme.png) | [Mobile View](mobile/07_customization_theme.png)
- **Settings:** Primary/Secondary color pickers, Classic vs Professional Dashboard switcher, Bangla vs English checkout toggle, Search hint keywords.

---

### Module 08: Promotional Carousel & Sliders
- **Route:** `/customization/all-carousel`
- **Component:** `CarouselComponent`
- **Visuals:** [Desktop View](desktop/08_customization_carousel.png) | [Mobile View](mobile/08_customization_carousel.png)
- **Table Model:** `IMAGE | NAME | URL | URL TYPE | SERIAL SORT | STATUS | ACTIONS`

---

### Module 09: Delivery Charge Engine
- **Route:** `/settings/delivery-charge`
- **Component:** `DeliverySettingsComponent`
- **Visuals:** [Desktop View](desktop/09_settings_delivery.png) | [Mobile View](mobile/09_settings_delivery.png)
- **Config Fields:** Inside City Charge (৳60), Outside City Charge (৳120), Free Delivery threshold, Extra Weight Charge per KG, Excluded categories.

---

### Module 10: Courier Logistics Integration
- **Route:** `/settings/courier`
- **Component:** `CourierSettingsComponent`
- **Visuals:** [Desktop View](desktop/10_settings_courier.png) | [Mobile View](mobile/10_settings_courier.png)
- **Supported Integrations:** Steadfast Courier, Pathao Courier, RedX (API Key, Secret, Webhook URL, Auto-booking).

---

### Module 11: Payment Gateway Setup
- **Route:** `/settings/payment-methods`
- **Component:** `PaymentMethodsComponent`
- **Visuals:** [Desktop View](desktop/11_settings_payment.png) | [Mobile View](mobile/11_settings_payment.png)
- **Supported Providers:** Cash on Delivery (COD), bKash Merchant API, Nagad, Shurjopay, SSLCommerz.

---

### Module 12: Fraud Check API & Circuit Breaker
- **Route:** `/settings/fraud-check`
- **Component:** `FraudCheckSettingsComponent`
- **Visuals:** [Desktop View](desktop/12_settings_fraud.png) | [Mobile View](mobile/12_settings_fraud.png)
- **Config Fields:** BD Courier / EcomSentrix API Key (Bearer Token), Auto-check during checkout toggle, High-risk cancellation threshold.

---

### Module 13: Customer Directory & CRM
- **Route:** `/users/all-users`
- **Component:** `UserManagementComponent`
- **Visuals:** [Desktop View](desktop/13_customers.png) | [Mobile View](mobile/13_customers.png)
- **Table Model:** `Image | Username | Email | Has Access | Status | Actions`

---

### Module 14: Daily Business Summary & Executive Report
- **Route:** `/reports/daily-business-summary`
- **Component:** `DailyReportComponent`
- **Visuals:** [Desktop View](desktop/14_reports_summary.png) | [Mobile View](mobile/14_reports_summary.png)
- **8 Financial Sections:** Order Overview, Sales & Income, Payment Mode Breakdown, Inventory Snapshot, Order Advance, Courier Performance, Customer Complaints/Remarks, Management Summary PDF Export.

---

### Module 15: Progressive Web App (PWA) App Setup
- **Route:** `/app-setup`
- **Component:** `AppSetupComponent`
- **Visuals:** [Desktop View](desktop/15_app_setup.png) | [Mobile View](mobile/15_app_setup.png)
- **Capabilities:** Direct PWA install prompts for Android & iOS, Web Push order notifications, App Icon & Manifest customization.

---

### Module 16: Settings Hub Grid (The 22-Card Central Control)
- **Route:** `/settings/all-settings`
- **Component:** `AllSettingsComponent`
- **Visuals:** [Desktop View](desktop/16_settings_all_hub.png) | [Mobile View](mobile/16_settings_all_hub.png)
- **Architectural Layout:** 3-column / 4-column responsive grid displaying cards with icon, description, and direct `[Manage]` links:
  1. **Delivery Charge** (`settings/delivery-charge`)
  2. **Advance Payment** (`settings/advance-payment`)
  3. **SMS Gateway** (`settings/sms`)
  4. **Courier** (`settings/courier`)
  5. **Fraud Check API** (`settings/fraud-check`)
  6. **Payment Methods** (`settings/payment-methods`)
  7. **Social Login** (`settings/social-login`)
  8. **Facebook Pixel** (`settings/facebook-pixel`)
  9. **TikTok Pixel** (`settings/tiktok-pixel`)
  10. **Google Tag Manager** (`settings/google-tag-manager`)
  11. **Facebook Catalog Feed** (`settings/facebook-catalog`)
  12. **Manage Offers** (`settings/manage-offer`)
  13. **Live Chat** (`settings/chat-manage`)
  14. **Currency & Country** (`settings/currency`)
  15. **Custom Domain** (`settings/add-custom-domain`)
  16. **Order Settings** (`settings/order-setting`)
  17. **Product Settings** (`settings/product-setting`)
  18. **Google Search Console** (`settings/google-console`)
  19. **Blog Settings** (`blog/blog-setting`)
  20. **Invoice Customizer** (`settings/invoice-setting`)
  21. **Reward Points** (`settings/reward-point`)
  22. **ePBX Auto Call** (`settings/epbx`)

---

### Module 17: Support Center & Ticket Management
- **Route:** `/support`
- **Component:** `SupportComponent`
- **Visuals:** [Desktop View](desktop/17_support_center.png) | [Mobile View](mobile/17_support_center.png)
- **Key Features:**
  - **Tabs:** `Live Support` and `Ticket Management`.
  - **Live Support Card:**
    - Agent Status Pill: `● অনলাইন আছেন` (Online).
    - Language switcher: `BD বাংলা`, `US English`, `IT Italiano`.
    - Features: Real-time Live Chat, Dedicated Support Team, Rapid Response (< 1 min), Secure Session.
    - Action: `[ লাইভ সাপোর্ট সেশন শুরু করুন ]` (Start Live Support Session button).
  - **Expert Team Widget:** Shows current active support agent ("Maruf - আপনাকে কিভাবে সাহায্য করতে পারি?"), online count ("৭ জন অনলাইন আছেন").
  - **Past Sessions Card:** Lists historical support interactions with status ("০৪ Jun, 2026 - 09:40 PM ★ রেটিং দিন").
  - **Ticket Management:** `[ + Create Ticket ]` dialog, priority tags, message thread.

---

### Module 18: Tutorials & Video Guide Knowledgebase
- **Route:** `/tutorial`
- **Component:** `TutorialComponent`
- **Visuals:** [Desktop View](desktop/18_tutorials.png) | [Mobile View](mobile/18_tutorials.png)
- **Key Features:**
  - Search bar with instant autocomplete.
  - Video Card Grid with embedded player modals and topic chips:
    - *Selcom Admin Panel শুরু করার সহজ গাইড (Mobile)*
    - *Selcom Admin Panel শুরু করার সহজ গাইড (Desktop)*
    - *How To Setup New Facebook Pixel & Connect With Website*
    - *Introducing Selcom's Official Support System*
    - *Delivery Option Manage As Your Business Need*
    - *Advance Payment System*
    - *Fraud Checker & Courier History*
    - *Order Language Manage (Bangla / English)*

---

### Module 19: New Release Changelog & Version Timeline
- **Route:** `/new-release-report`
- **Component:** `NewReleaseComponent`
- **Visuals:** [Desktop View](desktop/19_new_release_changelog.png) | [Mobile View](mobile/19_new_release_changelog.png)
- **Key Features:**
  - Search release notes by version, title, description.
  - **Platform Filters:** `All`, `Admin Panel`, `Website`, `API`.
  - **Type Filters:** `All`, `New Feature`, `Issue Solve`, `Enhancement`, `Internal Upgradation`.
  - **Interactive Timeline:**
    - Version 1.0.31 (New Theme Design Option Added, Design Upgradation, Bug Fixes)
    - Version 1.0.21 (Tablet Support, Facebook Catalog, Payment Improvements)
    - Version 1.0.19 (Order List enhancements, performance optimizations)

---

### Module 20: Store Owner Profile & Security
- **Route:** `/profile`
- **Component:** `ProfileComponent`
- **Visuals:** [Desktop View](desktop/20_profile_owner.png) | [Mobile View](mobile/20_profile_owner.png)
- **Form Structure:**
  - Role Badge: `Role: OWNER`.
  - Store Logo / Profile Avatar with `[Pick Image]` trigger.
  - `[Change Password]` modal button.
  - Personal Information: Name (`Bdeshio`), Username (`Bdeshio`), Created At, Updated At.
  - Contact Information: Phone Number (`01948-821476`), Email Address.
  - Action: `[Update Profile]` (primary button).

---

### Module 21: Admin Panel Version & OTA Update System
- **Component:** `SidebarPanelMetaComponent` (`.sidebar-panel-meta__card`)
- **Visuals:** [Detail Screenshot](desktop/21_admin_version_card_detail.png)
- **Features:**
  - Header: `info` icon + `Admin panel` title.
  - Meta Rows: `Current version: v1.0.47`, `Last update: 20 Aug 2026`.
  - Status Indicators:
    - Up-to-date state: `● Panel up to date` (green badge).
    - Update-available state: `● New update available` (orange badge) + `[Update now]` purple button.
  - Update Flow: Triggers Service Worker cache purge and automatic page refresh without session loss.

---

### Module 22-23: Damage Inventory Management
- **Routes:** `/damage/list`, `/damage/add`
- **Visuals:** [List View](desktop/22_damage_list.png) | [Add Form](desktop/23_damage_add.png)
- **Features:**
  - Track lost/damaged merchandise with financial cost loss calculation.
  - Product selection autocomplete, damaged quantity, reason dropdown (Broken in Transit, Expired, Factory Defect), approved staff signature.

---

### Module 24: Customer Reviews & Ratings
- **Route:** `/review`
- **Visuals:** [Desktop View](desktop/24_review_list.png) | [Mobile View](mobile/24_review_list.png)
- **Features:** Product association, customer 1-5 star ratings, review text, status toggle (Approved / Hidden), merchant response modal.

---

### Module 25: IP Block & Threat Protection
- **Route:** `/ip-block/all-ip-block`
- **Visuals:** [Desktop View](desktop/25_ip_block.png) | [Mobile View](mobile/25_ip_block.png)
- **Features:** Block abusive IP addresses, automated block logs from fraud check circuit breaker, single-click unblock action.

---

### Module 26: Media Gallery & Cloud Asset Manager
- **Route:** `/my-gallery`
- **Visuals:** [Desktop View](desktop/26_gallery.png) | [Mobile View](mobile/26_gallery.png)
- **Features:** Grid/List view of all uploaded store assets, bulk image uploader, instant copy CDN URL, image dimensions and file size inspect.

---

### Module 27: Additional Pages (Custom Store CMS)
- **Route:** `/additional-pages/page-list`
- **Visuals:** [Desktop View](desktop/27_additional_pages.png) | [Mobile View](mobile/27_additional_pages.png)
- **Features:** CMS page manager (About Us, Privacy Policy, Terms, Warranty & Returns) with slug routing and WYSIWYG editor.

---

### Module 28: Vendor Admin Staff & Roles
- **Route:** `/admin`
- **Visuals:** [Desktop View](desktop/28_admin_control.png) | [Mobile View](mobile/28_admin_control.png)
- **Features:** Multi-staff management, role assignment (Store Manager, Order Operator, Support Executive), access permissions matrix.

---

### Module 29-32: Deep Catalog Taxonomy (Sub-Categories, Brands, Tags)
- **Routes:**
  - `/catalog/all-sub-category` ➔ [Sub Categories](desktop/29_catalog_sub_categories.png)
  - `/catalog/all-child-category` ➔ [Child Categories](desktop/30_catalog_child_categories.png)
  - `/catalog/all-brand` ➔ [Brands](desktop/31_catalog_brands.png)
  - `/catalog/tag/all-tag` ➔ [Tags](desktop/32_catalog_tags.png)
- **Features:** 3-tier hierarchical category taxonomy, Brand showcase logos, Storefront search and SEO tagging system.

---

### Module 33-34: Landing Page Builder (Customizable & Fixed)
- **Routes:**
  - `/landing-page/all-customizable-landing-page` ➔ [Customizable Pages](desktop/33_landing_pages_customizable.png)
  - `/landing-page/all-fixed-landing-page` ➔ [Fixed Landing Pages](desktop/34_landing_pages_fixed.png)
- **Features:** High-converting single-product landing page templates with embedded direct-checkout forms, timers, and social proof widgets.

---

### Module 35: Asset Valuation & Accounting Reports
- **Route:** `/reports/asset-valuation`
- **Visuals:** [Desktop View](desktop/35_reports_asset_valuation.png) | [Mobile View](mobile/35_reports_asset_valuation.png)
- **Metrics:** Total Stock Quantity, Stock Value at Cost Price, Stock Value at Selling Price, Potential Gross Margin, Category-wise asset distribution.

---

### Module 36: Inventory Requisition
- **Route:** `/requisition/list`
- **Visuals:** [Desktop View](desktop/36_requisition_list.png) | [Mobile View](mobile/36_requisition_list.png)
- **Features:** Internal stock requisition requests, supplier demand forecasting, approval status workflow.

---

### Module 37-40: Supplier & Procurement Management
- **Routes:**
  - `/supplier/all-supplier` ➔ [Supplier Directory](desktop/37_supplier_all.png)
  - `/supplier/direct-purchase-entry` ➔ [Direct Purchase Entry](desktop/38_supplier_direct_purchase.png)
  - `/supplier/purchase-return-list` ➔ [Purchase Return](desktop/39_supplier_purchase_return.png)
  - `/supplier/total-purchase-history` ➔ [Purchase History](desktop/40_supplier_purchase_history.png)
- **Features:** Full B2B procurement workflow: Supplier ledger, invoice entries, cost tracking, return credit notes.

---

### Module 41: Physical Stock Adjustment
- **Route:** `/stock-adjustment/list`
- **Visuals:** [Desktop View](desktop/41_stock_adjustment.png) | [Mobile View](mobile/41_stock_adjustment.png)
- **Features:** Reconcile physical warehouse stock with digital database stock (+ / - adjustments with audit reasons).

---

### Module 42-59: Specialized Settings Specifications
All specialized setting screens captured in Desktop and Mobile:
- **42. Advance Payment (`/settings/advance-payment`):** Configure mandatory bKash/Nagad advance fee for outside-Dhaka orders or high-risk fraud flags. [Desktop View](desktop/42_settings_advance_payment.png)
- **43. SMS Gateway (`/settings/sms`):** Provider setup (ElitBuzz, Greenweb, BulkSMSBD), OTP configuration, automated order placement & shipping SMS templates. [Desktop View](desktop/43_settings_sms.png)
- **44. Social Login (`/settings/social-login`):** Google OAuth and Facebook Login client credentials. [Desktop View](desktop/44_settings_social_login.png)
- **45. Facebook Pixel (`/settings/facebook-pixel`):** Pixel ID, Conversions API (CAPI) Access Token, Test Event Code. [Desktop View](desktop/45_settings_facebook_pixel.png)
- **46. TikTok Pixel (`/settings/tiktok-pixel`):** TikTok Pixel ID and Events API configuration. [Desktop View](desktop/46_settings_tiktok_pixel.png)
- **47. Google Tag Manager (`/settings/google-tag-manager`):** Container ID (`GTM-XXXXXX`) injection. [Desktop View](desktop/47_settings_google_tag_manager.png)
- **48. Facebook Catalog (`/settings/facebook-catalog`):** Dynamic XML/CSV Product Feed URL generation for Meta Dynamic Product Ads. [Desktop View](desktop/48_settings_facebook_catalog.png)
- **49. Manage Offers (`/settings/manage-offer`):** Discount rules, flash sales, coupon codes, buy-X-get-Y. [Desktop View](desktop/49_settings_manage_offer.png)
- **50. Live Chat (`/settings/chat-manage`):** WhatsApp floating widget number, Facebook Messenger, Tawk.to property ID. [Desktop View](desktop/50_settings_chat_manage.png)
- **51. Currency & Country (`/settings/currency`):** Currency symbol (৳), currency position (Right/Left), decimal places, country default. [Desktop View](desktop/51_settings_currency.png)
- **52. Custom Domain (`/settings/add-custom-domain`):** Domain mapping, CNAME/A-record validation, SSL certificate provisioning. [Desktop View](desktop/52_settings_custom_domain.png)
- **53. Order Settings (`/settings/order-setting`):** Auto-confirm toggle, minimum cart threshold, duplicate order detection window (minutes). [Desktop View](desktop/53_settings_order_setting.png)
- **54. Product Settings (`/settings/product-setting`):** Stock badge thresholds, out-of-stock ordering behavior, SKU auto-generation. [Desktop View](desktop/54_settings_product_setting.png)
- **55. Google Search Console (`/settings/google-console`):** HTML verification tag injection. [Desktop View](desktop/55_settings_google_console.png)
- **56. Blog Settings (`/blog/blog-setting`):** Store blog enable/disable, comments moderation, author display. [Desktop View](desktop/56_settings_blog.png)
- **57. Invoice Setting (`/settings/invoice-setting`):** Merchant logo upload, signature upload, invoice prefix, terms & conditions footer text. [Desktop View](desktop/57_settings_invoice.png)
- **58. Reward Points (`/settings/reward-point`):** Loyalty reward system (Points earned per 100 spent, point cash conversion value). [Desktop View](desktop/58_settings_reward_points.png)
- **59. ePBX Auto Call (`/settings/epbx`):** Automated IVR voice call integration for order confirmation. [Desktop View](desktop/59_settings_epbx.png)

---

### Module 60-62: Mobile App Shell, Bottom Navigation & Notifications
- **60. Mobile Menu Drawer (`app-mobile-menu-drawer`):** [Mobile View](mobile/60_mobile_bottom_bar_menu_drawer.png) - Full-screen responsive slide-out drawer containing categorized links to all 62 modules.
- **61. Mobile Bottom Bar (`app-mobile-bottom-bar`):** 5-tab quick access bar (`Dashboard`, `Product`, `Order List`, `Notification`, `Menu`).
- **62. Notifications Center (`/notifications`):** [Desktop View](desktop/62_notifications.png) | [Mobile View](mobile/62_notifications.png) - Real-time activity feed for new orders, stock alerts, courier sync updates, and system broadcasts.

---

## 4. Master Prompt for Gemini Hydrogen (Google AI Studio)

You can copy and paste this complete prompt into **Google AI Studio** with **Gemini Hydrogen**:

```markdown
Hello Gemini Hydrogen,

We have completed an exhaustive, 100% complete reverse-engineering and audit of the Selcom Multi-Store SaaS Admin Platform. 

All 62 modules—including the Desktop layouts (1440x900) and Mobile PWA layouts (390x844 with 5-tab bottom navigation and mobile drawer), tables, forms, reactive models, and API boundaries—are documented in `.ai/selcom-audit/SELCOM_ANGULAR_MASTER_SPEC.md` with full screenshot proof in `.ai/selcom-audit/screenshots/`.

### Core Project Requirements:
1. **Architecture:** Angular 18+ Standalone Components (`provideRouter`), Signals for reactive state (`signal`, `computed`), Typed Reactive Forms (`FormBuilder`).
2. **UI Design:** Angular Material (`@angular/material`) styled with Tailwind CSS to match Selcom's clean purple/white aesthetic.
3. **PWA First:** Seamless mobile experience with top header, responsive KPI cards, and bottom navigation bar (`Dashboard`, `Product`, `Order List`, `Notification`, `Menu`).
4. **Key Modules to Build First:**
   - **Shell & Navigation:** Desktop Sidebar + Version Meta Box (`v1.0.47`) + Mobile Bottom Bar & Drawer.
   - **Module 01 & 02:** Real-time Dashboard (8 KPI cards + sales chart) & All Orders Table with status tabs and courier sync.
   - **Module 16:** The 22-Card Central Settings Grid (`/settings/all-settings`).
   - **Module 17, 18, 19, 20:** Support Center (with live support & agent card), Tutorials Grid, New Release Timeline, and Profile/Owner settings.

Please review the architectural specification and begin generating the root application shell and navigation structure!
```
