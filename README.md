<div align="center">

# 🍽️ Premium QR Menu SaaS
### Full-Stack QR Menu & Restaurant Management Platform

A full-stack SaaS application for digital restaurant menus, product management, customer feedback, dynamic QR generation, and operational analytics. Built with **Next.js 16**, **React 19**, **TypeScript**, **Tailwind CSS v4**, **Prisma 6**, and **Neon PostgreSQL**.

[![Next.js 16](https://img.shields.io/badge/Next.js-16-black?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org/)
[![React 19](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS v4](https://img.shields.io/badge/Tailwind_CSS-v4-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Prisma 6](https://img.shields.io/badge/Prisma-6-2D3748?style=for-the-badge&logo=prisma&logoColor=white)](https://www.prisma.io/)
[![Neon PostgreSQL](https://img.shields.io/badge/Neon-PostgreSQL-00E599?style=for-the-badge&logo=postgresql&logoColor=white)](https://neon.tech/)
[![Vercel](https://img.shields.io/badge/Deployed_on-Vercel-black?style=for-the-badge&logo=vercel&logoColor=white)](https://qr-menu-delta-weld.vercel.app)

<br />

[🚀 **Explore Live Customer Menu**](https://qr-menu-delta-weld.vercel.app) &nbsp;•&nbsp;
[⚡ **View Admin Portal Showcase**](#-admin-management-portal) &nbsp;•&nbsp;
[🏛️ **System Architecture Document**](docs/architecture/system_architecture.md)

</div>

---

> [!NOTE]
> **Portfolio Showcase & Architecture Case Study**  
> The production source code is maintained in a private repository. This public repository documents the system architecture, product functionality, engineering decisions, and visual flows for portfolio and technical interview review.

---

## 📱 Live Visual Showcase

Experience both sides of the platform: the customer's mobile ordering flow and the venue manager's administrative dashboard.

<div align="center">
  <table>
    <tr>
      <th align="center" width="50%">
        <h3>📱 Customer Experience</h3>
        <p><em>Dark mode, chef's highlights, food modal & drawer cart</em></p>
      </th>
      <th align="center" width="50%">
        <h3 id="-admin-management-portal">⚡ Admin Management Portal</h3>
        <p><em>Real-time analytics, category reordering & QR generator</em></p>
      </th>
    </tr>
    <tr>
      <td align="center" valign="top">
        <img src="docs/demo/customer-flow.gif" alt="Customer Menu Flow" width="300" style="border-radius: 14px; box-shadow: 0 8px 24px rgba(0,0,0,0.25);" />
        <br /><br />
        <a href="https://qr-menu-delta-weld.vercel.app"><strong>🔗 Launch Customer Menu</strong></a>
      </td>
      <td align="center" valign="top">
        <img src="docs/demo/admin-showcase.gif" alt="Admin Portal Showcase" width="300" style="border-radius: 14px; box-shadow: 0 8px 24px rgba(0,0,0,0.25);" />
        <br /><br />
        <em>Admin mutations are protected by session authentication. See feature gallery below for full walkthrough.</em>
      </td>
    </tr>
  </table>
</div>

---

## 👨‍💻 My Role

This project was designed, architected, and built entirely by **Eren Toksöz** as an independent full-stack development effort. Key areas of ownership:

- **Frontend & Mobile UX**: Engineered a mobile-first responsive interface with persistent dark/light theme switching (`next-themes`), sticky category scroll tracking, and a client-side slide-over cart drawer.
- **Server Architecture**: Implemented Next.js App Router architecture, leveraging React Server Components for data fetching and Server Actions for mutations and feedback collection.
- **Data Modeling & Storage**: Designed the relational PostgreSQL schema using Prisma ORM, implementing relational cascades, category order indexing, and connection pooling on Neon Serverless.
- **Authentication & Security**: Built session-based route and mutation guards using signed HS256 JWT tokens stored in HTTP-only cookies via `jose`.
- **Operations & Validation**: Configured Vercel continuous deployment and authored automated Playwright headless verification scripts to validate core customer and admin journeys.

---

## 💡 Key Engineering Decisions & Challenges

### 1. Server vs. Client Rendering Boundaries
Customer menu pages use **React Server Components (RSC)** to fetch categories and products directly on the server, eliminating client-side data fetching waterfalls and reducing the JavaScript bundle sent to mobile devices. Interactive capabilities—such as the cart drawer (`CartContext`), category selector tabs, and theme toggling—are scoped to lightweight Client Components at the leaves of the render tree.

### 2. Spam-Resistant Feedback Without Mandatory Login
To capture guest feedback without forcing customers to create an account, the `submitFeedback` Server Action computes a composite SHA-256 fingerprint from the client's IP, User-Agent, and the targeted product ID. A 6-hour rate-limiting window prevents duplicate ballot stuffing while returning an idempotent response to preserve a friction-free guest experience.

### 3. Relational Modeling & Manual Category Ordering
Restaurant menus require custom display order rather than simple alphabetical or timestamp sorting. The database model maintains an explicit `order` integer column on `Category`. A dedicated Server Action updates category positions atomically, enabling restaurant managers to re-prioritize seasonal or high-margin menus during service.

### 4. Client-Side Cart vs. Server-Side Mutations
The shopping cart operates entirely in client-side memory through React Context (`CartContext`), enabling instant quantity adjustments and total price recalculations without network latency. Server-side communication is reserved for state mutations requiring database persistence (such as product feedback and admin CRUD operations).

---

## 🏛️ System Architecture

A modular full-stack architecture separating client presentation, server-side orchestration, and serverless relational persistence. Detailed documentation is available in [docs/architecture/system_architecture.md](docs/architecture/system_architecture.md).

```mermaid
graph TD
    subgraph Client Layer
        A["📱 Guest Smartphone (Mobile Web)"]
        B["💻 Venue Manager (Desktop / Mobile Admin)"]
    end

    subgraph Application Layer ["Next.js 16 (Vercel Serverless)"]
        C["⚡ Server Components (Server-Rendered Menu Catalog)"]
        D["🛒 Client State (CartContext & Local UI State)"]
        E["🔄 Server Actions (Feedback, Product & Category Mutations)"]
        F["🔒 Auth Guard (Signed Session JWT via jose)"]
    end

    subgraph Data Layer
        G["📦 Prisma ORM (Connection Pooling)"]
        H[("🐘 Neon Serverless PostgreSQL")]
    end

    A -->|1. Request Menu| C
    A -->|2. Local Cart Interactions| D
    A -->|3. Submit Feedback| E
    B -->|4. Authenticated Admin Actions| F
    F -->|5. Authorized Mutations| E
    C -->|Read Queries| G
    E -->|Write Mutations| G
    G -->|TLS Connection Pool| H
```

---

## 🗄️ Relational Database Schema

The database model is normalized around restaurant tenancy, ordered categories, product metadata, and customer feedback:

```mermaid
erDiagram
    Restaurant ||--o{ Category : "has many"
    Restaurant ||--o{ Product : "owns"
    Category ||--o{ Product : "contains"
    Product ||--o{ Feedback : "receives"

    Restaurant {
        string id PK
        string name
        string slug UK
        datetime createdAt
    }

    Category {
        string id PK
        string name
        int order
        string restaurantId FK
    }

    Product {
        string id PK
        string name
        string description
        decimal price
        string image
        boolean isAvailable
        boolean isFeatured
        int featuredOrder
        int calories
        string allergens
        string meatOrigin
        boolean hasAlcohol
        boolean hasPork
        string categoryId FK
        string restaurantId FK
        datetime createdAt
    }

    Feedback {
        string id PK
        string productId FK
        string type
        string comment
        string voterHash
        datetime createdAt
    }
```

---

## ✨ Core Product Capabilities

### 🍽️ Customer-Facing Application
- **Mobile-First Menu Browsing**: Responsive interface with sticky category navigation that follows the user's scroll position.
- **Theme Switching**: Dark and light mode toggle powered by `next-themes` with persistent state across visits.
- **Slide-Over Cart Drawer**: Client-side cart supporting instant quantity increments, item removal, and total price calculation.
- **Featured Items Carousel**: Horizontal touch-enabled showcase for signature dishes and chef's recommendations.
- **Friction-Free Feedback**: Like/Dislike ratings with optional comments, rate-limited via device fingerprinting.

### 🥗 Food Information & Transparency Features
Designed with awareness of Turkish digital restaurant menu guidelines (Tarım ve Orman Bakanlığı):
- **Per-Item Calorie Information**: Caloric values (kcal) configured per item and displayed in detail modals.
- **Allergen Indicators**: Structured badges for common dietary allergens (Gluten, Dairy, Nuts, etc.).
- **Meat Origin Sourcing**: Transparent sourcing field (Beef, Chicken, Lamb, or Non-Meat).
- **Dietary Indicators**: Visible flags for alcohol or pork presence to support guest dietary preferences.

### 📊 Business Admin Portal
- **Operational Analytics**: Overview metrics including scan frequency, daily visitor trends, and item satisfaction ratios.
- **Dynamic Category Reordering**: Drag-and-drop / sequence ordering to reorganize menu sections on demand.
- **Product Inventory Management**: Control item availability, adjust pricing, and edit nutritional disclosures in real time.
- **Table QR Code Generator**: Generate table-specific SVG/PNG QR codes configured for on-premise printing.
- **Session-Based Authentication**: Protected admin routes with signed HS256 session cookies.

---

## 📸 Feature Gallery

<div align="center">
  <table>
    <tr>
      <th align="center" colspan="2"><h3>🖥️ Administrative Dashboard & Controls</h3></th>
    </tr>
    <tr>
      <td align="center" width="50%">
        <img src="docs/screenshots/admin-dashboard-desktop.png" alt="Admin Analytics Dashboard" width="420" style="border-radius: 8px;" />
        <br />
        <strong>Operational Overview & Traffic Trends</strong>
      </td>
      <td align="center" width="50%">
        <img src="docs/screenshots/admin-products-desktop.png" alt="Product Inventory Management" width="420" style="border-radius: 8px;" />
        <br />
        <strong>Product Catalog & Inventory Controls</strong>
      </td>
    </tr>
    <tr>
      <th align="center" colspan="2"><h3>📱 Mobile Guest & Administrative Flows</h3></th>
    </tr>
    <tr>
      <td align="center" width="50%">
        <img src="docs/screenshots/customer-item-modal.png" alt="Food Transparency Modal" width="280" style="border-radius: 10px;" />
        <br />
        <strong>Transparency Modal (Calories, Allergens, Sourcing)</strong>
      </td>
      <td align="center" width="50%">
        <img src="docs/screenshots/customer-cart-mobile.png" alt="Slide-Over Cart" width="280" style="border-radius: 10px;" />
        <br />
        <strong>Client-Side Slide-Over Cart</strong>
      </td>
    </tr>
    <tr>
      <td align="center" width="50%">
        <img src="docs/screenshots/admin-categories-mobile.png" alt="Category Reordering" width="280" style="border-radius: 10px;" />
        <br />
        <strong>Dynamic Category Ordering Interface</strong>
      </td>
      <td align="center" width="50%">
        <img src="docs/screenshots/admin-qr-code-mobile.png" alt="Table QR Generator" width="280" style="border-radius: 10px;" />
        <br />
        <strong>Table QR Code Generation Engine</strong>
      </td>
    </tr>
  </table>
</div>

---

## 🛠️ Technology Stack & Engineering Rationale

| Layer | Technology | Engineering Rationale |
| :--- | :--- | :--- |
| **Framework** | [Next.js 16](https://nextjs.org/) | App Router architecture; Server Components for server-rendered menu data and Server Actions for data mutations |
| **UI Library** | [React 19](https://react.dev/) | Utilizing React 19 concurrent features, transitions, and component-level state |
| **Language** | [TypeScript 5](https://www.typescriptlang.org/) | Static type safety spanning database models, server action contracts, and UI components |
| **Styling** | [Tailwind CSS v4](https://tailwindcss.com/) | Utility-first CSS engine enabling responsive layouts, CSS variables, and native dark mode |
| **Database** | [Neon PostgreSQL](https://neon.tech/) | Serverless PostgreSQL with managed connection pooling suited for serverless execution |
| **ORM** | [Prisma 6](https://www.prisma.io/) | Type-safe query building, automated migrations, and schema modeling |
| **Authentication** | [jose](https://github.com/panva/jose) | Edge-compatible JWT signing and verification for HTTP-only session cookies |
| **Notifications** | [Sonner](https://sonner.emilkowal.ski/) | Lightweight toast notifications for user action feedback |
| **Icons** | [Lucide React](https://lucide.dev/) | Consistent, accessible icon set with tree-shaking support |
| **Deployment** | [Vercel](https://vercel.com/) | Continuous deployment pipeline with serverless compute and global asset distribution |

---

## 📊 Project Status & Testing

- **Current Status**: `Completed / Portfolio-Ready / Independently Developed`
- **Quality & Verification**:
  - User journeys validated via headless **Playwright** browser automation scripts and cross-device mobile testing.
  - End-to-end flows tested across dark and light themes, touch viewports, and varied network latency conditions.
  - *Engineering Roadmap*: Integration of automated unit test suites (Jest/Vitest) and CI pipeline checks is planned as the next milestone.

---

## 📁 Repository Structure

```
premium-qr-menu-showcase/
├── README.md                           # Project case study & technical overview
└── docs/
    ├── architecture/
    │   └── system_architecture.md      # Deep-dive architecture & data flow documentation
    ├── demo/
    │   ├── customer-flow.gif           # Optimized mobile customer flow animation (~5 MB)
    │   └── admin-showcase.gif          # Optimized admin dashboard flow animation (~3 MB)
    └── screenshots/                    # High-resolution desktop and mobile UI captures
```

---

## 👨‍💻 About the Developer

**Eren Toksöz** — Software Engineer  
Open to Software Engineering, Full-Stack, and Frontend Development opportunities in Türkiye and remote international teams.

- **GitHub**: [@ErenTzz](https://github.com/ErenTzz)
- **LinkedIn**: [linkedin.com/in/erentoksoz](https://www.linkedin.com/in/erentoksoz)
- **Live Demo**: [qr-menu-delta-weld.vercel.app](https://qr-menu-delta-weld.vercel.app)

*Commercial inquiries regarding custom hospitality implementations or platform licensing are welcome via LinkedIn or email.*

---

<div align="center">
  <sub>Copyright © 2026 Eren Toksöz. All Rights Reserved. Prepared for portfolio review and technical evaluation.</sub>
</div>
