# Provabook – Public Showcase Repository

This repository curates the public-facing story of **Provabook**, a textile operations control center that unifies foreign orders, sampling, production, and logistics inside one command cockpit. It deliberately omits proprietary source code and instead highlights the architecture, methodology, and implementation depth delivered in the private repo.

---

## 1. Elevator Pitch
- **Frontline visibility:** Orders, production KPIs, incidents, and shipments stay live so merchandisers never wait for updates.
- **Role-aware workflows:** Admin, manager, merchandiser, QA/logistics, and on-floor personas get tailored dashboards plus server-enforced permissions.
- **Document-first:** Tech packs, approvals, PI/LC packets, and incident dossiers stay pinned to each order with secure cloud storage.

---

## 2. Screens & Visuals
| Dashboard & Navigation | Samples Workspace | Local Orders Board |
| --- | --- | --- |
| ![Dashboard](images/Screenshot%202025-12-20%20214058.jpg) | ![Samples](images/Screenshot%202025-12-20%20214151.jpg) | ![Local Orders](images/Screenshot%202025-12-20%20214210.jpg) |

---

## 3. Feature Map
1. **Foreign Order Management** – Order/style hierarchy with buyer metadata, Google Cloud document stack, stage pipelines, and approval timelines.
2. **Development & Sampling** – Lab Dip → PP flows with versioned submissions, courier logging, resubmission plans, and inline photo viewer.
3. **Financials** – Multi-version Proforma Invoices plus LC trackers with expiry alerts, discrepancy notes, and attachment vault.
4. **Local Production & Daily Metrics** – Floor tracker with throughput, cut/finish states, inline notes, and KPI widgets on dashboard landing.
5. **Incidents & Blockers** – Capture rejections, breakdowns, delays, and action plans with ownership + due dates + evidence uploads.
6. **Shipments & Logistics** – Packing list / invoice / AWB uploads, ETD/ETA tracking, delivery confirmation workflow.
7. **Notifications & Activity** – Real-time unread counter with 30s polling and role-based subscriptions for incidents, approvals, LC milestones.

---

## 4. Architecture Overview
```
┌───────────────────────────────────────────────────────────────┐
│                         Frontend (Next.js 14)                 │
│  - App Router + TypeScript + Tailwind + shadcn/ui             │
│  - React Query + Zustand for async + session state            │
│  - Axios interceptor auto-appends DRF-friendly trailing slash │
│  - Auth store keeps JWT + user profile                        │
└───────────────▲───────────────────────────────┬───────────────┘
                │                               │
        HTTPS (JWT Bearer)                      │
                │                               │
┌───────────────┴───────────────────────────────▼───────────────┐
│                   Backend (Django 5 + DRF)                    │
│  - Custom User (email login) + SimpleJWT auth                 │
│  - Role-based permissions (admin, manager, merchandiser)      │
│  - Orders v1 + line-level approvals + stats endpoints         │
│  - Modular apps: samples, financials, production, shipments   │
│  - Swagger / ReDoc auto docs                                  │
└───────────────▲───────────────────────────────┬───────────────┘
                │                               │
        Google Cloud Storage                    │
        (documents & photos)                    │
                │                               │
┌───────────────┴───────────────┐       ┌───────┴───────────────┐
│ PostgreSQL (DigitalOcean)     │       │ Background jobs ready │
│ - Orders / Styles / Lines     │       │ - Celery-ready seeds  │
│ - Samples / Financials / KPI  │       │ - Notifications hooks │
└───────────────────────────────┘       └───────────────────────┘
```

### Key Backend Modules
- **Authentication:** JWT login/register, profile management, password changes, role management.
- **Orders:** Atomic `OrderLine` model holds quantities, prices, approvals, and local production metrics with automatic sequence assignment.
- **Samples / Financials / Production / Shipments:** App scaffolding with serializers, views, and filters ready for domain rollout.
- **Core Utilities:** Exception handling, logging, Google Cloud storage helpers, presigned document URLs, notification hooks.

### Key Frontend Constructs
- **Dashboard Landing:** Gradient cards for module navigation, authenticated header with unread counter, CTA to deep dashboard.
- **Orders Workspace:** Cached list with filter persistence, inline order line approvals, sample photo viewer, deletion approvals.
- **Local Production Board:** Derived stage badges (Pre-Yarn → Delivered), greige/yarn calculators, production summary progress bars.
- **Global Providers:** Auth store (Zustand), React Query hydration, API client with automatic trailing slashes and token injection.

---

## 5. Data & Workflow Methodology
- **Order → Style → Line Hierarchy:** Every order can fan into styles, each style spawns multiple `OrderLine` combinations (style/color/CAD). Commercial data (quantity, pricing, approvals, ETD/ETA) lives at the line level, enabling precise approvals and production tracking.
- **Line-Level Approvals:** Approvals (Lab Dip, Strike-Off, Handloom, PP, AOP, Quality, Bulk Swatch, Price) store status history and aggregate upward to order-level dashboards.
- **Local Production Telemetry:** Lines capture yarn/greige math, knitting/dyeing/cutting/sewing milestones, plus automatic timeline calculations once yarn arrives.
- **Document-First Flows:** Orders and lines attach files (PI packets, CADs, incident photos) stored in Google Cloud Storage with presigned URLs so the UI streams assets without exposing bucket credentials.
- **Notification Loop:** Django signals and the notifications app maintain unread counts surfaced on the Next.js navbar with 30-second polling.

---

## 6. Implementation Highlights
1. **Order Line Refactor:** Brand-new `OrderLine` model, serializers, and migrations push approvals + pricing down to the atomic unit, unlocking multi-line approvals and accurate production math.
2. **Custom Approval Gates:** Managers configure bespoke gate names; frontend auto-generates meaningful abbreviations and renders per-line gate badges.
3. **Local Production Metrics:** Backend aggregates production entries + delivered quantities, while frontend derives stage badges and renders knitting/dyeing/finishing progress bars with weighted averages.
4. **Cache-Friendly Orders UI:** SessionStorage caching (data + scroll + expanded rows) provides instant reloads while background refreshes keep data current.
5. **Photo Viewer Fix:** Sampling modal now lives outside conditional render blocks, preventing remount flicker when merchandisers cycle through assets.
6. **Environment Hardening:** Default `.env` references the managed DigitalOcean PostgreSQL replica for parity, and helper scripts clean corrupted Next.js caches post-pull.

---

## 7. Recent Engineering Focus
- `fix: add customGates to order list serializer and refresh data on gate changes`
- `fix: use meaningful abbreviations for custom approval gates`
- `feat: implement custom approval gates for order lines`
- `feat: update frontend file upload limit to 60MB`
- `feat: increase max file upload size to 60MB`

These commits demonstrate ongoing investment in order-line fidelity, UX polish, and production-readiness constraints (e.g., heavier document uploads).

---

## 8. Public Repository Contents
```
public_release/
├── README.md                 # You are here
└── images/                   # Marketing screenshots referenced above
```

For source code access, reach out to the Provabook engineering team. This showcase repo is optimized for portfolio, RFP, and stakeholder demos without leaking proprietary implementations.
