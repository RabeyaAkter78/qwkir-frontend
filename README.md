# Qwikr E-Commerce Frontend Platform

An enterprise-grade e-commerce storefront designed for high performance, scalability, and a seamless user experience. As the lead frontend engineer, my primary focus was architecting a resilient frontend application and establishing a robust API integration layer that ensures real-time data synchronization, aggressive caching, and a flawless checkout pipeline.

## 🔗 Live Application
**Live Link:** https://qwikr.us/

## 🛠 Technical Stack & Architectural Decisions

We selected a modern, highly optimized stack to guarantee both developer velocity and maximum end-user performance.

- **Framework:** Next.js 15.4 (App Router) - Chosen for aggressive Server-Side Rendering (SSR) and Static Site Generation (SSG), vital for e-commerce SEO and initial initial load speeds.
- **UI Library:** React 19.1 - Leveraging the latest concurrent features for smoother UI transitions.
- **State Management & Data Fetching:** Redux Toolkit & RTK Query - Implemented to handle complex client-side state (like cart management) and provide an intelligent, declarative API layer with built-in caching and request deduplication.
- **Styling & UI Components:** Tailwind CSS v4 paired with Ant Design (`antd`) - This hybrid approach gives us rapid utility-class styling for layout, while relying on battle-tested AntD components for complex interactive elements (modals, tables).
- **Animations:** Framer Motion & AOS - Strategic micro-interactions that elevate the perceived performance and premium feel of the platform without blocking the main thread.
- **Security:** `jwt-decode` and custom route protection middleware to ensure secure, token-based authentication.

## 📂 Architecture & Folder Structure

I designed the directory structure to enforce separation of concerns, making the codebase predictable and highly maintainable for scaling the team.

```text
src/
├── app/                  # Next.js App Router (Pages, Layouts, Routing)
│   ├── (auth)/           # Authentication flows (Login, Signup, OTP)
│   ├── (withNavFooter)/  # Main e-commerce flows (Products, Cart, Checkout)
│   └── utils/            # Global utilities and helper functions
├── components/           # Reusable UI Architecture
│   ├── Providers/        # Global context providers (Redux, Theme, Toast)
│   ├── Shared/           # Atomic components (Buttons, Inputs, Cards)
│   └── pages/            # Page-specific composite components
├── redux/                # Global State & API Integration Layer
│   ├── api/              # Base API configuration (Interceptors, Base URL)
│   ├── features/         # Domain-driven slices and RTK Query endpoints
│   │   ├── auth/         # Authentication state & API
│   │   ├── cartApi/      # Cart mutations & queries
│   │   ├── productsApi/  # Product fetching & filtering
│   │   └── ...           # (Orders, Profile, Categories, etc.)
│   └── store.ts          # Centralized store configuration with persistence
└── assets/               # Static assets (Images, Icons)
```

## 🚀 Core Features

- **Comprehensive Auth Pipeline:** Secure login, registration, password recovery, and OTP-based verification.
- **Dynamic Product Catalog:** High-performance product listing with server-side filtering, category navigation, and optimized image delivery.
- **Robust Cart & Checkout:** Persistent shopping cart state across sessions using `redux-persist`, seamlessly transitioning into a multi-step, secure checkout process.
- **User Dashboard & Order History:** Detailed user profiles with real-time order tracking and historical data.
- **Review & Rating System:** Integrated product review capabilities with immediate UI optimistic updates.

## ⚡ Performance Optimizations

To meet rigorous web vital metrics, I implemented several key performance strategies:

1. **Intelligent Caching:** Leveraged RTK Query's caching mechanisms to prevent redundant network requests. Product data is cached aggressively, while volatile data (like cart totals) triggers immediate cache invalidation.
2. **Component-Level Code Splitting:** Heavy libraries and below-the-fold components are dynamically imported.
3. **Optimized Asset Delivery:** Next.js Image component handles automatic format selection (WebP/AVIF), resizing, and lazy loading.
4. **Debounced API Calls:** Implemented debouncing on search and filtering endpoints to reduce server load and prevent UI stuttering.

## 🧠 Challenging Features & Technical Solutions

### 1. Synchronizing Persistent Cart State with the Server
**The Challenge:** Users expect their cart to remain intact even if they close the tab, but the server must remain the ultimate source of truth to prevent inventory mismatch or price tampering during checkout.

**The Solution:** I engineered a hybrid state approach using `redux-persist` for immediate client-side availability, paired with background synchronization via RTK Query mutations. When a user logs in, a custom middleware reconciles the local storage cart with the server's cart database, resolving conflicts gracefully before the user reaches the checkout phase.

### 2. Complex RTK Query Cache Invalidation
**The Challenge:** E-commerce platforms have highly interdependent data. For instance, successfully placing an order needs to immediately clear the cart, update order history, and potentially adjust available product stock—all without requiring a hard page refresh.

**The Solution:** I architected a robust tag-based invalidation system within our `baseApi`. By assigning specific tags (e.g., `['Cart', 'Order', 'Product']`), a single mutation (like `createOrder`) automatically invalidates the relevant tags. This triggers background refetches precisely where needed, ensuring UI consistency with minimal network payload.

### 3. Secure & Frictionless OTP Authentication Flow
**The Challenge:** Managing the state between the initial login request and the subsequent OTP validation, while ensuring security tokens aren't exposed prematurely or lost on an accidental reload.

**The Solution:** Implemented a multi-stage authentication slice in Redux. The initial API call yields a temporary session identifier. Using `react-otp-input`, the user inputs the code which fires the secondary validation endpoint. Only upon full verification are the final JWT access and refresh tokens parsed via `jwt-decode` and persisted securely, triggering a seamless redirect to the protected application layout.
