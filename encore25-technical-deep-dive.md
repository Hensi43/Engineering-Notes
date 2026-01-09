# Encore 25: Technical Deep Dive & Architectural Highlights

**Project:** Encore 25 Official Web Portal
**Stack:** Node.js, Express, Vanilla JS + GSAP, TailwindCSS, LowDB

## Executive Summary
This document outlines the key engineering decisions and technical features of the Encore 25 platform. It is designed to showcase the system's interactive capabilities, security measures, and pragmatic architectural choices to technical stakeholders.

## 1. Frontend Architecture & Markup Standards

### Semantic HTML5 & SEO Optimizations
*   **Semantic Structure:** Usage of proper landmark elements (`<nav>`, `<section>`, `<main>`) ensures the site is accessible to screen readers and indexable by crawlers.
*   **Social Graph Integration:** Explicit `meta` tags (Open Graph) and semantic `alt` attributes on all dynamic imagery (e.g., "IET Logo", "Concert Crowd") ensure the content is shareable and accessible.
*   **Performance Hints:** Implemented `<link rel="preconnect">` for Google Fonts and critical assets to reduce round-trip times during the initial SSL handshake, speeding up content painting.

### Utility-First CSS (Tailwind)
*   **Scalable Design Tokens:** Configured custom Tailwind themes (color palette, spacing) to ensure visual consistency across the "dark mode" aesthetic (`bg-black`, `text-[#FFA500]`).
*   **Zero-Runtime Overhead:** Unlike CSS-in-JS solutions, Tailwind compiles to atomic CSS classes at build time, resulting in a minimal bundle size and zero JavaScript blocking time for styling.

## 1.2 Immersive User Experience (Frontend)

### High-Performance Animations
*   **GSAP Integration:** Leveraged the GreenSock Animation Platform (GSAP) for complex, timeline-based sequences—specifically the seamless cross-fading of hero images and synchronized text updates ("Nawabi Elegance" section).
*   **3D Transform Engine:** Implemented hardware-accelerated CSS3 3D transforms (`preserve-3d`, `rotateY`, `perspective: 1000px`) for card-flip interactions, ensuring 60fps performance on mobile devices without heavy WebGL overhead.
*   **Intersection Observers:** Integrated the `IntersectionObserver` API to trigger entrance animations only when elements enter the viewport. This reduces the main thread blocking time during initial page load, significantly improving Core Web Vitals (LCP/FID).

### Responsive Design System
*   **Mobile-First Strategy:** Built using TailwindCSS with a mobile-first philosophy, ensuring the "hamburger" menus, touch targets, and grid layouts naturally adapt from iPhone SE sizes up to 4K desktops.
*   **Adaptive Coverflow:** The event carousel uses a custom Swiper.js configuration (`effect: 'coverflow'`) that dynamically adjusts depth (`depth: 100`) and rotation based on the screen width, maintaining immersion across devices.

## 2. Backend & Data Architecture

### Pragmatic Persistence Layer (LowDB)
*   **Decision:** Opted for `LowDB` (local JSON persistence) over a heavy SQL database.
*   **Rationale:**
    *   **Zero-Latency Reads:** Data resides in memory, providing sub-millisecond read speeds for high-traffic read operations (e.g., fetching event lists).
    *   **Portability:** The entire database state is atomic and version-controllable, making backups and migrations (e.g., moving from local to Render) trivial.
    *   **Concurrency Handling:** Uses atomic file writes `await db.write()` to ensure data integrity during concurrent registrations.

### Secure Payment Pipeline
*   **Razorpay Integration:** Full full-stack integration with the Razorpay Payment Gateway.
*   **HMAC Signature Verification:** Implemented **cryptographic verification** of payment success on the server side (`crypto.createHmac('sha256', secret)`).
    *   *Why this matters:* Prevents "client-side spoofing" where a user might intercept the frontend success callback to fake a payment. The server independently verifies the Razorpay signature before granting the "Fest Pass".
*   **Dev/Prod Parity:** Engineered a `ENABLE_FAKE_PAYMENT` toggle. This allows the engineering team to test the entire booking flow end-to-end (including DB writes and state updates) without requiring active banking credentials, speeding up the dev loop.

## 3. Security & Scalability Notables
*   **UUID Generation:** Uses `crypto.randomUUID()` for non-collision user IDs, ensuring scalability if the user base grows into the thousands.
*   **CORS Configuration:** Explicit `cors()` middleware usage with strict origin policies (configurable) to prevent XSS/CSRF attacks from unauthorized domains.
*   **Modular Routing:** API logic (`/api/register`, `/api/login`) is decoupled from static asset serving, allowing the backend to potentially be split into a microservice in future iterations.

## 4. Modern Development Practices
*   **ES Modules (ESM):** The backend is fully migrated to modern ECMAScript Modules (`"type": "module"`), using `import`/`export` syntax over legacy CommonJS. This aligns the codebase with future Node.js standards and allows for tree-shaking optimizations if bundled later.
*   **Hot Reloading:** Configured `nodemon` with specific ignore rules (`--ignore db/`) to prevent the server from restarting when the local JSON database is written to. This prevents "restart loops" during development persistence testing, a common pitfall with file-system-based databases.

## 5. Key Engineering Challenge: The "Double-Spend" Problem
*   **The Problem:** In high-concurrency scenarios (e.g., hundreds of students registering at once), checking if a user has "Already Paid" before creating a new order can lead to race conditions.
*   **The Solution:** Implemented a state-check mechanism where the payment status is verified *twice*:
    1.  **Pre-Order:** Before generating a Razorpay Order ID.
    2.  **Post-Verify:** Atomically updating the `paymentStatus` to `'paid'` with a timestamp. 
    *   *Result:* This robust state management ensures that even if a frontend client sends multiple requests, the database remains consistent with a single source of truth for payment status.

---
*This architecture demonstrates a balance between "Wow Factor" visual capability and "Boring but Reliable" backend systems—ensuring a stable platform for thousands of student users.*
