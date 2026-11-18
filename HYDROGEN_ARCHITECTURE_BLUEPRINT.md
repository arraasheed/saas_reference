# Modern Angular SaaS Store Admin — Architectural Blueprint for Gemini Hydrogen

> **Target AI Engineer:** Gemini 4 Hydrogen (Google AI Studio)  
> **Mission:** Build a high-performance, modern, contemporary Multi-Store SaaS Admin Portal inspired by the functional depth and PWA ergonomics of Selcom, but with a cleaner, next-gen architectural foundation (Angular 19+ Standalone, Signals, Tailwind CSS).  
> **Source Benchmark:** Selcom Store Admin (`https://admin.bdeshio.com/`)  
> **Author:** Antigravity AI Engine (Local DeepMind Agent)

---

## 1. How Selcom Works Under the Hood (Analysis & Reverse-Engineering)

Selcom is a single-vendor store administration system engineered with **Angular (Angular 17/18)** and **Angular Material**, configured as an installable **Progressive Web Application (PWA)**.

### A. Key Architectural Characteristics:
1. **PWA Dual-Layout Navigation:**
   - **Desktop (>= 1024px):** 260px sticky sidebar with collapsible accordion submenus, top utility header (search, live support pill, language toggle, notification stack), and high-density data tables.
   - **Mobile PWA (< 768px):** Mimics a native mobile app. It strips heavy table headers in favor of responsive cards and provides a **5-Tab Sticky Bottom Navigation Bar** (`Dashboard`, `Product`, `Order List`, `Notification`, `Menu`) where `Menu` opens a full-screen drawer.
2. **Routing & Code Splitting:**
   - Feature-based lazy loading (`loadChildren` / standalone `loadComponent`).
   - Active route tree cached in client `localStorage` (`vendor_sidebar_menu_cache_v4_*`) for instant menu hydration before network handshakes.
3. **Reactive Form Heavy:**
   - Almost every screen (Products, Delivery Settings, Courier, Advance Payment, Fraud Check) relies on complex reactive forms with nested field arrays and dynamic validation.
4. **Local Ecommerce Realities (Bangladesh Context):**
   - High COD (Cash on Delivery) volume.
   - Integrated Fraud Detection (tracking delivery success rate per customer phone number).
   - Multi-Courier API integrations (Steadfast, Pathao, RedX) with one-click status syncing.
   - Advance payment circuit breaker (demanding bkash/nagad advance for suspicious/risky orders).

---

## 2. Our Modern Architectural Blueprint (How WE Will Build It)

We do **NOT** want to copy their exact styling 1:1. Instead, we want to build a **contemporary, clean, hyper-optimized SaaS platform** that adopts their functional depth and workflow ergonomics, but built with cutting-edge Angular standards.

```
+---------------------------------------------------------------------------------------------------------+
|                                    NEXT-GEN ANGULAR 19+ ARCHITECTURE                                    |
+---------------------------------------------------------------------------------------------------------+
|  REACTIVITY CORE         |  Angular Signals (`signal()`, `computed()`, `effect()`) — Zoneless Friendly   |
|  ROUTING & DEFERRABILITY |  Standalone Components + `@defer (on viewport; prefetch on idle)`             |
|  DESIGN SYSTEM           |  Tailwind CSS v3/v4 + Angular Material / CDK primitives + Lucide Icons        |
|  STATE MANAGEMENT        |  Lightweight Signal Stores (Service-with-Signals pattern)                     |
|  DATA ARCHITECTURE       |  Typed DTOs + Strict Reactive Forms + HttpInterceptor with Bearer Auth        |
|  PWA LAYER               |  `@angular/service-worker` + Manifest + Offline Cache + Web Push API         |
+---------------------------------------------------------------------------------------------------------+
```

### Core Architecture Pillars for Hydrogen:

### Pillar 1: Pure Standalone & Signals Reactivity (No NgModules, No Zone.js Overhead)
- Use Angular Standalone components across 100% of the codebase (`provideRouter`, `provideHttpClient(withInterceptors(...))`).
- Replace complex RxJS `BehaviorSubject` boilerplate with **Angular Signals**:
  ```typescript
  // Modern Signal Store Pattern
  @Injectable({ providedIn: 'root' })
  export class OrderStateService {
    readonly orders = signal<Order[]>([]);
    readonly filter = signal<OrderStatus>('ALL');
    readonly isLoading = signal<boolean>(false);

    readonly filteredOrders = computed(() => {
      const current = this.filter();
      return current === 'ALL' 
        ? this.orders() 
        : this.orders().filter(o => o.status === current);
    });
  }
  ```

### Pillar 2: Contemporary SaaS Visual Aesthetic (Modern, Sleek, Not Cluttered)
- **Palette:** Clean slate/zinc neutral backdrop (`#F8FAFC` / `#0F172A`), crisp white surface cards with subtle border strokes (`border-slate-200/80`), and a refined primary accent (Indigo `#6366F1` or Violet `#7C3AED`) instead of outdated gradients.
- **Typography:** Inter or Plus Jakarta Sans with distinct hierarchy (`font-semibold text-slate-900`, `text-xs text-slate-500`).
- **Icons:** Sharp vector iconography via `@ng-icons/lucide` or Lucide Angular.
- **Micro-Interactions:** Smooth skeleton loaders during lazy data fetch, toast alerts via hot-toast/sonner, and optimistic UI updates for instant toggle actions.

### Pillar 3: Dual-Mode Ergonomics (Responsive PWA)
- **Desktop View:** Collapsible 240px sidebar, search with `Ctrl+K` command palette, fast keyboard shortcuts.
- **Mobile PWA View:**
  - Sticky bottom tab bar (`Dashboard`, `Products`, `Orders`, `Alerts`, `More`).
  - Haptic-ready touch targets (min 44px height).
  - Quick status chips horizontally scrollable on mobile.
  - Floating Action Button (FAB) for instant order creation or product addition.

---

## 3. High-Value Functional Modules & Domain Model

Gemini Hydrogen should structure the application into 6 logical domain boundaries:

```
src/app/
├── core/                  # Auth, Interceptors, Guards, Layout shell, Signal Stores
│   ├── layout/            # Desktop Sidebar, Mobile Bottom Bar, Header, Drawer
│   ├── services/          # API Clients, PWA Update Service, WebSocket/Push
│   └── state/             # Global Tenant & User Signals
├── features/
│   ├── dashboard/         # 8 KPI Metrics, Real-time Charts, Recent Activity
│   ├── orders/            # All Orders, Incomplete Carts, Invoice Modal, Courier Sync
│   ├── products/          # Catalog, Variants Matrix, Inventory, Add/Edit wizard
│   ├── settings/          # Central Settings Hub (22 feature cards)
│   ├── support-tutorial/  # Live Agent Chat, Knowledgebase, Release Changelog
│   └── procurement/       # Suppliers, Stock Adjustments, Damage tracking
└── shared/                # UI primitives (DataTable, StatCard, Badge, Modal, FormFields)
```

### Essential Workflow Schemas to Implement:

#### 1. The Orders Core Engine:
- **Status Lifecycle:** `PENDING` ➔ `CONFIRMED` ➔ `PROCESSING` ➔ `SHIPPED` ➔ `DELIVERED` / `CANCELLED` / `HOLD` / `RETURNED`.
- **Integrated Actions:**
  - Single-click courier push (generate tracking ID from Steadfast/Pathao).
  - Customer trust score badge (e.g. `94% Delivery Rate - Low Risk`).
  - Print Packing Slip / Thermal 4x6 Label.

#### 2. The Settings Control Hub:
- Instead of burying settings in endless submenus, use a **Central Hub Grid** (`/settings/all-settings`) displaying 22 feature cards.
- Each card displays its live status (e.g. `Steadfast: Active`, `Fraud Check: Enabled`, `Free Delivery: > ৳1000`).

#### 3. Fraud Detection Circuit Breaker:
- Automatic validation on customer phone number.
- High cancellation history flagged in red (`High Risk Order: Advance Payment Required`).

#### 4. Support & Changelog Hub:
- In-app Support Center (`/support`) with active agent status and ticket escalation.
- Release Changelog (`/new-release-report`) with tag-filtered timeline showing version improvements.

---

## 4. Phase-by-Phase Roadmap for Gemini Hydrogen

Gemini Hydrogen should execute the project in 4 clear, structured phases:

### Phase 1: Application Shell & PWA Layout
- Setup Angular Standalone Router with lazy routes.
- Implement responsive Layout Shell (`SidebarComponent` for desktop, `BottomNavComponent` for mobile, `HeaderComponent` with Search and Profile).
- Build the bottom version status widget with OTA service-worker update trigger.

### Phase 2: Analytics Dashboard & Real-Time Orders
- Implement `DashboardComponent` with 8 KPI stat cards and responsive Chart.js/ApexCharts integration.
- Implement `OrderListComponent` with status tabs, search/date filtering, status badges, and expandable row details.

### Phase 3: Product Catalog & Variant Matrix
- Build `ProductListComponent` with fast inline stock editing and low-stock alarms.
- Build `AddProductComponent` with multi-tab reactive form (General, Pricing, Multi-image upload, Dynamic Attribute Matrix for Size/Color).

### Phase 4: Settings Hub & Local Commerce Integrations
- Build the 22-card `AllSettingsComponent` grid.
- Implement Delivery Charge logic (Inside/Outside city calculation).
- Implement Courier & Fraud Check API configuration panels.
- Build Support & New Release Changelog screens.

---

## 5. Direct Master Prompt to Copy-Paste into Google AI Studio

```markdown
Hello Gemini Hydrogen,

We are designing a next-generation Multi-Store SaaS Admin Portal for modern eCommerce merchants.

### Architectural Blueprint:
- **Framework:** Angular 19+ (Standalone Components, `provideRouter`, Signals for reactive state, typed Reactive Forms).
- **Styling:** Tailwind CSS + Angular CDK / Material primitives. Contemporary, clean SaaS aesthetic (zinc/slate background, crisp card borders, violet/indigo accent).
- **Responsive PWA:** Dual-layout system (Desktop 240px collapsible sidebar + Mobile 5-tab sticky bottom navigation bar with full drawer).
- **Core Functional Depth:**
  1. Real-time Dashboard with 8 KPI cards & sales trend charts.
  2. Dense Orders Management table with status filter tabs, customer fraud trust badges, and courier status sync.
  3. Product Catalog with dynamic variant matrix (Size/Color/SKU/Stock).
  4. Central Settings Hub (22 feature cards covering Delivery, Courier, Fraud Check, Pixels, Payment, Domain).
  5. In-app Support Center & Version Release Timeline.

Please refer to the architecture blueprint and start by generating Phase 1: The Root Standalone Layout Shell (Desktop Sidebar + Mobile Bottom Navigation + Top Header + Version Meta Widget) with full TypeScript and Tailwind code.
```
