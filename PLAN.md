# E-Commerce Learning Project — Execution Plan

This plan adapts `guide.md` to your chosen stack and breaks the project into
**epics → issues → tasks** you can work through one at a time, plus a rough
timeline assuming **~20h/week**.

## Stack (differences from guide.md)


| Layer               | guide.md            | This plan                                                                                                       |
| --------------------- | --------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Frontend            | Vite + React + TS   | **Next.js (App Router) + React + TS**, client-side rendering ("use client" pages, no SSR data-fetching for now) |
| Frontend data layer | Axios + Zustand     | **Zustand + native `fetch`** (no React Query for now)                                                           |
| Backend             | Express + TS        | **Express + TS** (unchanged)                                                                                    |
| DB / ORM            | PostgreSQL + Prisma | unchanged                                                                                                       |
| Auth                | JWT                 | unchanged                                                                                                       |
| Payments            | Stripe              | unchanged                                                                                                       |
| Infra               | Docker Compose      | unchanged                                                                                                       |

Database schema, API design, security checklist, and Docker setup from
`guide.md` (sections 3, 4, 6, 8, 11) remain valid as-is — refer back to those
when implementing.

### Adapted directory structure (frontend only)

```
frontend/
├── app/
│   ├── layout.tsx                # Root layout (providers, header/footer)
│   ├── page.tsx                  # Home ("/")
│   ├── shop/page.tsx              # Shop ("/shop")
│   ├── product/[id]/page.tsx      # Product detail
│   ├── cart/page.tsx
│   ├── checkout/page.tsx
│   ├── login/page.tsx
│   ├── register/page.tsx
│   ├── profile/page.tsx
│   └── admin/
│       ├── layout.tsx             # Admin guard + sidebar
│       ├── page.tsx               # Dashboard
│       ├── products/page.tsx
│       └── orders/page.tsx
├── components/                    # ProductCard, Header, CartItem, AdminLayout...
├── services/                       # fetch wrappers: auth.ts, products.ts, cart.ts, orders.ts
├── store/                           # Zustand: authStore, cartStore
├── hooks/                            # useAuth, useCart, useProducts
├── types/                             # product.ts, order.ts, user.ts
└── utils/                              # validators, helpers
```

Everything else (backend layout, prisma schema, docker-compose) follows
`guide.md` sections 2, 3, 6 directly.

---

## Epics & Issues

### Epic 0 — Project Setup & Environment

**Goal:** Working skeleton: Next.js app, Express app, Postgres in Docker, all talking to each other.

- [X] Init git repo + base folder structure (`frontend/`, `backend/`, `docker/`)
- [ ] Scaffold Next.js app (App Router, TS, Tailwind)
- [ ] Scaffold Express + TS backend (tsconfig, ts-node-dev, basic `app.ts`/`server.ts`)
- [ ] Add Prisma, connect to Postgres via Docker
- [ ] Write `docker-compose.yml` (postgres, backend, frontend) per guide.md §6
- [ ] `.env.example` for both frontend and backend
- [ ] Health-check route (`GET /api/health`) + confirm frontend can call it

**Est:** 8–10h

---

### Epic 1 — Database Design & Models

**Goal:** Schema defined, migrated, and seeded.

- [ ] Write `schema.prisma` (users, categories, products, orders, order_items) per guide.md §3
- [ ] Run `prisma db push` / first migration
- [ ] Write seed script (`prisma/seed.ts`) — sample categories + products + an admin user
- [ ] Verify data via Prisma Studio

**Est:** 6–8h

---

### Epic 2 — Authentication

**Goal:** Register, login, JWT-protected routes, working on both ends.

Backend:

- [ ] `auth.service` — register (bcrypt hash), login, `me`
- [ ] `auth.controller` + `auth.routes` (`/api/auth/register`, `/login`, `/me`)
- [ ] JWT middleware (`middleware/auth.ts`) + admin role middleware
- [ ] Input validation (Zod) on register/login

Frontend:

- [ ] `authStore` (Zustand) — token + user, persisted to localStorage
- [ ] Register page + form validation
- [ ] Login page
- [ ] `useAuth` hook + protected route wrapper
- [ ] Header reflects auth state (login/logout, links)

**Est:** 14–18h

---

### Epic 3 — Product Catalog

**Goal:** Browse, search, and view products.

Backend:

- [ ] `GET /api/products` with pagination, search, category filter
- [ ] `GET /api/products/:id`
- [ ] `GET /api/categories`
- [ ] Admin-only `POST/PUT/DELETE /api/products` (used later by admin panel, build now)

Frontend:

- [ ] `ProductCard` component
- [ ] Home page — featured products
- [ ] Shop page — grid + filters + pagination
- [ ] Product detail page

**Est:** 16–20h

---

### Epic 4 — Shopping Cart

**Goal:** Add/remove/update items, persisted client-side.

- [ ] `cartStore` (Zustand, persisted) — add/remove/update qty/clear/total
- [ ] "Add to cart" from ProductCard and Product detail
- [ ] Cart page — line items, qty controls, totals
- [ ] Cart icon + item count badge in Header

**Est:** 8–10h

---

### Epic 5 — Checkout & Payments

**Goal:** Real Stripe test-mode payment flow producing an order.

Backend:

- [ ] `order.service` — create order from cart + Stripe session/payment intent
- [ ] `POST /api/orders` (checkout)
- [ ] Stripe webhook endpoint (`/api/webhooks/stripe`) — mark order paid, signature verification
- [ ] `GET /api/orders`, `GET /api/orders/:id`

Frontend:

- [ ] Checkout page — Stripe Elements form (requires auth)
- [ ] Order confirmation / success page
- [ ] Handle payment failure states

**Est:** 14–18h

---

### Epic 6 — Order History & Profile

**Goal:** Logged-in users can see their account and past orders.

- [ ] Order history page (`/profile` or `/orders`)
- [ ] Order detail view
- [ ] Profile page — view user info
- [ ] (Optional) `PATCH /api/orders/:id/cancel`

**Est:** 6–8h

---

### Epic 7 — Admin Panel

**Goal:** Admin can manage products and orders.

- [ ] Admin route guard (role check, redirect non-admins)
- [ ] `AdminLayout` (sidebar nav)
- [ ] Admin Dashboard — basic stats (order count, revenue, low-stock products)
- [ ] Admin Products page — table + create/edit/delete forms (uses Epic 3 endpoints)
- [ ] Admin Orders page — list + update order status
- [ ] Backend: `GET /api/admin/orders`, `PUT /api/admin/orders/:id/status`

**Est:** 16–20h

---

### Epic 8 — Testing & Security Pass

**Goal:** Confidence the core flow works and is reasonably secure.

- [ ] Backend unit tests (Jest) — auth service, cart/price calculations
- [ ] Backend integration tests (Supertest) — register → login → browse → checkout
- [ ] Frontend component tests (RTL) — cart logic, auth-guarded routes
- [ ] Walk through Security Checklist from guide.md §11 and fix gaps (rate limiting, CORS, helmet, etc.)

**Est:** 8–12h

---

### Epic 9 — Polish (pick 2–3, optional / stretch)

Pick whichever interests you most; don't feel obligated to do all:

- [ ] Image upload for products (Cloudinary)
- [ ] Infinite scroll on Shop page
- [ ] Email order receipts (Nodemailer)
- [ ] Discount/promo codes
- [ ] Product reviews & ratings

**Est:** 4–8h each

---

### Epic 10 — Deployment

**Goal:** App runs outside your machine.

- [ ] Production Dockerfiles (multi-stage, per guide.md §6)
- [ ] Deploy (e.g., Railway/Render/Fly.io for backend+db, Vercel for Next.js frontend, or full Docker on a VPS)
- [ ] Configure production env vars + HTTPS
- [ ] Final README / setup docs

**Est:** 8–12h

---

## Timeline (20h/week)


| Week(s)                                   | Epic                             | Hours          | Milestone                                        |
| ------------------------------------------- | ---------------------------------- | ---------------- | -------------------------------------------------- |
| 1                                         | Epic 0 — Setup                  | 8–10h         | Frontend ↔ backend ↔ DB all running via Docker |
| 1–2                                      | Epic 1 — DB & Models            | 6–8h          | Schema migrated + seeded                         |
| 2–3                                      | Epic 2 — Auth                   | 14–18h        | Register/login works end-to-end                  |
| 3–5                                      | Epic 3 — Product Catalog        | 16–20h        | Can browse, search, filter products              |
| 5–6                                      | Epic 4 — Cart                   | 8–10h         | Cart persists and updates totals                 |
| 6–8                                      | Epic 5 — Checkout & Stripe      | 14–18h        | Test payment creates a real order                |
| 8                                         | Epic 6 — Orders & Profile       | 6–8h          | User can see order history                       |
| **— MVP milestone (Epics 0–6) —**      |                                  | **~70–92h**   | **~4–5 weeks**                                  |
| 9–12                                     | Epic 7 — Admin Panel            | 16–20h        | Admin can manage products/orders                 |
| 12–13                                    | Epic 8 — Testing & Security     | 8–12h         | Core flow covered by tests                       |
| **— Full app milestone (Epics 0–8) —** |                                  | **~95–125h**  | **~5–6.5 weeks**                                |
| 13–15                                    | Epic 9 — Polish (2–3 features) | 8–24h         | A couple of "nice to have" features              |
| 15–17                                    | Epic 10 — Deployment            | 8–12h         | App live on the internet                         |
| **— Total —**                           |                                  | **~110–160h** | **~6–8 weeks**                                  |

> Note: these are *implementation* hours. Since this project is also for
> learning Next.js/Express, expect to spend extra time reading docs and
> experimenting — a realistic full timeline is closer to **10–14 weeks**
> at 20h/week. Treat the hour estimates as a floor, not a deadline.

## Suggested order of attack

1. Epic 0 → 1 → 2: get the foundation and auth solid — everything else builds on this.
2. Epic 3 → 4: products and cart are mostly frontend-heavy, good for Next.js practice.
3. Epic 5 → 6: the trickiest backend logic (Stripe, orders) — tackle once you're comfortable.
4. Epic 7: admin panel reuses patterns from 2–6, should go faster.
5. Epic 8 throughout, but do a dedicated pass before moving to polish/deploy.
6. Epic 9 and 10 are optional/flexible — stop earlier if you've met your learning goals.
