# Encore 26: Technical Deep Dive & Architectural Highlights

**Project:** Encore 26 Official Web Portal
**Stack:** Next.js 16 (App Router), Node.js, Prisma ORM, MySQL, TailwindCSS v4, Framer Motion
**Infrastructure:** NetWarden (Traffic Control), Vercel/Render (Deployment)

## Executive Summary
This document outlines the key engineering decisions and technical features of the Encore 26 platform. It highlights the transition to a robust relational architecture, the implementation of advanced traffic control systems, and the focus on immersive, high-performance UI.

## 1. Frontend Architecture & Modern Web Standards

### Next.js 16 & Server Components
*   **App Router Paradigm:** leveraged React Server Components (RSC) to reduce client-side bundle size. Critical rendering logic happens on the server, ensuring fast First Contentful Paint (FCP).
*   **Streaming & Suspense:** Implemented granular loading states with `Suspense` boundaries, allowing parts of the UI (like the Event Dashboard) to load progressively without blocking the entire page.

### Utility-First CSS (Tailwind v4)
*   **Scalable Design Tokens:** Configured custom Tailwind themes to ensure visual consistency across the "dark mode" aesthetic (`bg-black`, `text-[#D4AF37]` for gold accents).
*   **Zero-Runtime Overhead:** Tailwind v4's new engine compiles styles instantly, keeping the CSS bundle minimal and preventing layout shifts.

## 1.2 Immersive User Experience

### High-Performance Animations
*   **Framer Motion:** Replaced heavy external libraries with `framer-motion` for layout animations and shared element transitions.
*   **3D Transform Engine:** Implemented hardware-accelerated CSS3 3D transforms for card-flip interactions and the "Hero Parallax", ensuring 60fps performance on mobile devices.
*   **Intersection Observers:** Lazy-loaded heavy assets and animations only when elements enter the viewport, improving Core Web Vitals.

### Responsive Design System
*   **Mobile-First Strategy:** Built using a mobile-first philosophy, ensuring layouts adapt naturally from iPhone SE sizes up to 4K desktops.

## 2. Backend & Data Architecture

### Robust Relational Data (Prisma + MySQL)
*   **Decision:** Migrated from `LowDB` to **MySQL** managed via **Prisma ORM**.
*   **Rationale:**
    *   **Referential Integrity:** Enforced strict relationships between Users, Carts, Orders, and Teams.
    *   **Type Safety:** Prisma's generated client provides end-to-end type safety, reducing runtime errors.
    *   **Scalability:** MySQL handles concurrent write operations (e.g., hundreds of students registering simultaneously) significantly better than file-based systems.

### Complex Schema Features
*   **Atomic Transactional Orders:** The `Order` system links securely to `Payment` verification. Statuses transition atomically (`PENDING` -> `PAID`) to prevent race conditions.
*   **Team Management:** Implemented a robust Team system where a `User` can lead a `Team` (`@@unique([eventSlug, leaderId])`) or be a member, with strict constraints to prevent duplicate registrations.
*   **Ledger-Based Rewards:** The `CoinHistory` table acts as an immutable ledger for "Nawabi Coins", tracking every earning (Instagram task) and spending event with a clear audit trail.

### Secure Payment Pipeline
*   **Razorpay Integration:** Full stack integration with Razorpay.
*   **HMAC Signature Verification:** Implemented cryptographic verification (`crypto.createHmac`) on the server to prevent client-side payment spoofing.
*   **Double-Verification Strategy:**
    1.  **Pre-Order:** Verify stock/eligibility before generating an Order ID.
    2.  **Post-Verify:** Atomically update `Order` status and grant `Team` access only after successful signature validation.

## 3. Infrastructure & Traffic Control (NetWarden)

### NetWarden Integration
*   **Role:** Users custom Python-based traffic control system (`netwarden`) to manage network stability during high-traffic events (e.g., registration opening).
*   **Traffic Classification:** Uses `TrafficClassifier` to identify packet types based on SNI and flow patterns.
*   **Dynamic Throttling:** The `SystemThrottler` enforces bandwidth policies (`configs/policy.yaml`), ensuring that admin tools and SSH sessions (`class: high`) get priority over bulk downloads or background syncs (`class: low`).
*   **Fairness Algorithms:** Implements logic to "punish hogs"—automatically throttling IP addresses or internal processes that exceed fair-use bandwidth limits (`hog_threshold_mbps`).

## 4. Security & Scalability

-   **CUID Generation:** Switched to CUIDs (`@default(cuid())`) for primary keys. These are collision-resistant and time-sortable, making them superior to standard UUIDs for database indexing.
-   **Role-Based Access Control (RBAC):** Middleware checks `User.role` (USER vs CA vs ADMIN) to protect sensitive API routes (`/api/admin/*`).
-   **Rate Limiting:** Implemented API rate limiting to prevent abuse of the registration and login endpoints.

## 5. Encore 26 Updates & Enhancements (Jan 2026)

### Core Refinements
*   **Authentication Overhaul:** Pivoted to a robust credentials-only authentication flow.
*   **Admin Dashboard:** Comprehensive `/admin` panel for live user management, payment verification, and manual overrides.

### Visual Polish
*   **"Nawabi Elegance" Theme:** A distinct visual identity centered around Lucknow's heritage.
*   **Interactive Elements:** Custom golden cursor, parallax scrolling, and "Roomi Darwaza" inspired architectural UI elements.

---
*This architecture demonstrates a shift from a prototype-based stack to a production-ready, scalable relational system capable of handling thousands of concurrent users.*
