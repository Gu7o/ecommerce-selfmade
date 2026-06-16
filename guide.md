# E-Commerce Platform — Project Plan & Development Guide

## 1. Architecture Overview

```
┌─────────────────────────────────────────────────┐
│                  Docker Network                   │
│                                                     │
│  ┌───────────┐     ┌──────────────┐     ┌──────────┐│
│  │   Vite    │◄──►│ Express.js   │◄──►│PostgreSQL││
│  │ React/TS  │     │ Node.js + TS │     │          ││
│  │ (Frontend)│     │ (Backend)    │     │          ││
│  └───────────┘     └──────────────┘     └──────────┘│
│         ▲                 ▲                         │
│         │                 │                         │
│      HTTPS / TLS      Stripe API                   │
│                                                   │
└─────────────────────────────────────────────────┘
```

**Stack:** Vite + React + TypeScript | Express.js + Node.js + TypeScript | PostgreSQL | Docker | JWT Auth | Stripe Payments

---

## 2. Directory Structure

```
ecommerce-platform/
├── frontend/                        # Vite + React + TypeScript
│   ├── public/                      # Static assets (favicon, images)
│   ├── src/
│   │   ├── components/              # Reusable UI components
│   │   │   ├── Header.tsx           # Navigation bar with cart icon
│   │   │   ├── ProductCard.tsx      # Product display card
│   │   │   ├── CartItem.tsx         # Cart line item
│   │   │   ├── AdminLayout.tsx      # Admin sidebar layout
│   │   │   └── ...                  # Other shared components
│   │   ├── pages/                   # Route-level page components
│   │   │   ├── Home.tsx             # Landing / featured products
│   │   │   ├── Shop.tsx             # Product listing with filters
│   │   │   ├── ProductDetail.tsx    # Single product view
│   │   │   ├── Cart.tsx             # Shopping cart
│   │   │   ├── Checkout.tsx         # Payment flow (Stripe)
│   │   │   ├── Login.tsx            # User login
│   │   │   ├── Register.tsx         # User registration
│   │   │   ├── Profile.tsx          # User account dashboard
│   │   │   ├── OrderHistory.tsx     # Past orders
│   │   │   └── admin/               # Admin panel routes
│   │   │       ├── Dashboard.tsx    # Sales overview, stats
│   │   │       ├── Products.tsx     # Product CRUD
│   │   │       ├── Orders.tsx       # Order management
│   │   │       └── Users.tsx        # User management (optional)
│   │   ├── services/                # API client layer
│   │   │   ├── api.ts               # Axios instance with interceptors
│   │   │   ├── auth.ts              # Auth API calls
│   │   │   ├── products.ts          # Product API calls
│   │   │   ├── orders.ts            # Order API calls
│   │   │   └── cart.ts              # Cart API calls
│   │   ├── store/                   # State management (Zustand)
│   │   │   ├── authStore.ts         # Auth state + token
│   │   │   ├── cartStore.ts         # Local cart state
│   │   │   └── adminStore.ts        # Admin-specific state
│   │   ├── hooks/                   # Custom React hooks
│   │   │   ├── useAuth.ts           # Auth context/guard hook
│   │   │   ├── useCart.ts           # Cart operations
│   │   │   └── useProducts.ts       # Product fetching
│   │   ├── routes/                  # Route definitions + guards
│   │   │   ├── PublicRoutes.tsx     # Unprotected routes
│   │   │   ├── ProtectedRoutes.tsx  # Auth-required routes
│   │   │   └── AdminRoutes.tsx      # Admin-only routes
│   │   ├── types/                   # Shared TypeScript types
│   │   │   ├── product.ts           # Product, Category interfaces
│   │   │   ├── order.ts             # Order, OrderItem types
│   │   │   └── user.ts              # User type definitions
│   │   ├── utils/                   # Helpers
│   │   │   ├── validators.ts        # Form/validation helpers
│   │   │   └── helpers.ts           # Formatting (currency, dates)
│   │   ├── App.tsx                  # Root component + router
│   │   └── main.tsx                 # Entry point
│   ├── index.html
│   ├── vite.config.ts
│   ├── tsconfig.json
│   └── package.json
├── backend/                         # Express.js + TypeScript
│   ├── src/
│   │   ├── config/                  # Configuration
│   │   │   ├── database.ts          # PostgreSQL connection
│   │   │   ├── stripe.ts            # Stripe SDK init
│   │   │   └── jwt.ts               # JWT config
│   │   ├── middleware/              # Express middlewares
│   │   │   ├── auth.ts              # JWT verification
│   │   │   ├── admin.ts             # Admin role check
│   │   │   ├── error.ts             # Error handling
│   │   │   └── validation.ts        # Request validation (Zod)
│   │   ├── routes/                  # API route definitions
│   │   │   ├── auth.routes.ts       # /api/auth/*
│   │   │   ├── products.routes.ts   # /api/products/*
│   │   │   ├── orders.routes.ts     # /api/orders/*
│   │   │   └── cart.routes.ts       # /api/cart/*
│   │   ├── controllers/             # Route handlers
│   │   │   ├── auth.controller.ts   # Register, login, me
│   │   │   ├── products.controller.ts
│   │   │   ├── orders.controller.ts
│   │   │   └── cart.controller.ts
│   │   ├── services/                # Business logic layer
│   │   │   ├── auth.service.ts      # User creation, password hashing
│   │   │   ├── product.service.ts   # CRUD + search
│   │   │   ├── order.service.ts     # Order processing + Stripe
│   │   │   └── cart.service.ts      # Cart operations
│   │   ├── models/                  # Database model definitions (Prisma)
│   │   │   ├── User.ts              # id, email, password_hash, role
│   │   │   ├── Product.ts           # name, description, price, stock...
│   │   │   ├── Category.ts          # name, slug
│   │   │   ├── Order.ts             # user_id, status, total, address
│   │   │   └── OrderItem.ts         # order_id, product_id, qty, price
│   │   ├── utils/                   # Helpers
│   │   │   ├── crypto.ts            # bcrypt helpers
│   │   │   ├── logger.ts            # Logging utility
│   │   │   └── response.ts          # Standardized API responses
│   │   ├── app.ts                   # Express app setup + middleware
│   │   └── server.ts                # Server start (listen)
│   ├── prisma/                      # Prisma ORM
│   │   ├── schema.prisma            # Database schema definition
│   │   └── seed.ts                  # Seed script for dev data
│   ├── tests/                       # Backend tests (Jest)
│   │   ├── unit/                    # Unit tests for services/controllers
│   │   └── integration/             # API endpoint tests (supertest)
│   ├── .env                         # Environment variables
│   ├── .env.example                 # Template with all vars
│   ├── tsconfig.json
│   └── package.json
├── docker/                          # Dockerfiles per service
│   ├── frontend/Dockerfile          # Multi-stage: build + nginx serve
│   ├── backend/Dockerfile           # Node.js production image
│   └── postgres/Dockerfile          # Custom PostgreSQL init script
├── docker-compose.yml               # Full orchestration
├── .env.example                     # Root-level env template
└── README.md                        # Project documentation
```

---

## 3. Database Schema (PostgreSQL + Prisma)

### Tables & Relationships

```
Users ──────< Orders >──── OrderItems >── Products
  │                                    ▲
  ├──── CartItems                      │
  └──── Orders (order history)          │
                                       │
Categories <───────────────────────────┘
```

### Schema Details

**users**


| Column        | Type                 | Description            |
| --------------- | ---------------------- | ------------------------ |
| id            | UUID (PK)            | Unique identifier      |
| email         | VARCHAR(255) UNIQUE  | Login credential       |
| password_hash | VARCHAR              | bcrypt hashed password |
| role          | ENUM('user','admin') | Default: 'user'        |
| created_at    | TIMESTAMP            | Registration time      |

**categories**


| Column | Type                | Description             |
| -------- | --------------------- | ------------------------- |
| id     | UUID (PK)           | Unique identifier       |
| name   | VARCHAR(100)        | Display name            |
| slug   | VARCHAR(100) UNIQUE | URL-friendly identifier |

**products**


| Column      | Type                    | Description                      |
| ------------- | ------------------------- | ---------------------------------- |
| id          | UUID (PK)               | Unique identifier                |
| name        | VARCHAR(255)            | Product name                     |
| description | TEXT                    | Full product description         |
| price       | DECIMAL(10,2)           | Price in cents/stored as decimal |
| images      | JSONB[]                 | Array of image URLs              |
| stock       | INTEGER                 | Available quantity (min: 0)      |
| category_id | UUID (FK → categories) | Product category                 |
| active      | BOOLEAN                 | Soft toggle for listing          |
| created_at  | TIMESTAMP               | Creation time                    |

**orders**


| Column            | Type               | Description             |
| ------------------- | -------------------- | ------------------------- |
| id                | UUID (PK)          | Unique identifier       |
| user_id           | UUID (FK → users) | Buyer                   |
|                   |                    |                         |
|                   |                    |                         |
| total             | DECIMAL(10,2)      | Computed from items     |
| shipping_address  | JSONB              | Full address snapshot   |
| stripe_payment_id | VARCHAR            | Stripe charge reference |
| created_at        | TIMESTAMP          | Order creation time     |

**order_items**


| Column     | Type                  | Description            |
| ------------ | ----------------------- | ------------------------ |
| id         | UUID (PK)             | Unique identifier      |
| order_id   | UUID (FK → orders)   | Parent order           |
| product_id | UUID (FK → products) | Purchased product      |
| quantity   | INTEGER               | Units bought           |
| price      | DECIMAL(10,2)         | Price at purchase time |

---

## 4. Backend API Design

### Authentication (`POST /api/auth/*`)

```
POST    /api/auth/register          → { user }           Create account
POST    /api/auth/login             → { token, user }     Login + JWT
GET     /api/auth/me                → { user }            Current user info
POST    /api/auth/logout            → { success }         Clear session (optional)
```

### Products (`GET/POST /api/products/*`)

```
GET     /api/products                    → { products, total }       List with pagination
GET     /api/products/:id                → { product }               Single product detail
GET     /api/products?search=shoes&category=electronics&page=1      Search + filters
POST    /api/products                    → { product }               Admin: Create
PUT     /api/products/:id                → { product }                Admin: Update
DELETE  /api/products/:id                → { success }                 Admin: Delete
```

### Cart (`GET/POST/PUT/DELETE /api/cart/*`)

```
GET    /api/cart                        → { items, total }      Current user cart
POST   /api/cart                        → { item }               Add product to cart
PUT    /api/cart/:productId             → { item }                Update quantity
DELETE /api/cart/:productId             → { success }              Remove from cart
DELETE /api/cart/clear                  → { success }              Empty cart
```

### Orders (`POST/GET /api/orders/*`)

```
POST   /api/orders                      → { order }               Checkout (creates Stripe session)
GET    /api/orders                      → { orders }                User's order history
GET    /api/orders/:id                  → { order }                 Order detail
PATCH  /api/orders/:id/cancel           → { success }                Cancel order (user)

# Admin endpoints (require admin role)
GET    /api/admin/orders                → { orders }                 All orders
PUT    /api/admin/orders/:id/status     → { order }                  Update status
```

### Stripe Webhook

```
POST   /api/webhooks/stripe             Handles payment success/failure events
         → Updates order status to 'paid' on successful charge
         → Rolls back cart + creates order record
```

---

## 5. Frontend Pages & Flow

### User-Facing Pages


| Page           | Route          | Description                            | Auth Required?                  |
| ---------------- | ---------------- | ---------------------------------------- | --------------------------------- |
| Home           | `/`            | Hero banner, featured products         | No                              |
| Shop           | `/shop`        | Product grid with filters + pagination | No                              |
| Product Detail | `/product/:id` | Full product view, add to cart         | No                              |
| Cart           | `/cart`        | Review items, adjust quantities        | No (but checkout requires auth) |
| Checkout       | `/checkout`    | Stripe payment form                    | Yes                             |
| Login          | `/login`       | Sign in form                           | No                              |
| Register       | `/register`    | Create account                         | No                              |
| Profile        | `/profile`     | View/edit info, order history          | Yes                             |

### Admin Panel Pages (prefix: `/admin`)


| Page      | Route             | Description                      | Auth Required?   |
| ----------- | ------------------- | ---------------------------------- | ------------------ |
| Dashboard | `/admin`          | Sales stats, recent orders       | Yes + admin role |
| Products  | `/admin/products` | Product CRUD table               | Yes + admin role |
| Orders    | `/admin/orders`   | Order management, status updates | Yes + admin role |

### State Management: Zustand

```typescript
// authStore.ts — persisted JWT token + user data
const useAuthStore = create((set) => ({
  user: null,
  token: localStorage.getItem('token'),
  setUser: (user: User, token: string) => { /* persist */ },
  clearUser: () => { /* remove from storage */ }
}))

// cartStore.ts — local cart state until checkout
const useCartStore = create((set) => ({
  items: [],
  addItem: (product) => { /* add or increment qty */ },
  removeItem: (productId) => { /* filter out */ },
  updateQuantity: (productId, qty) => { /* update */ },
  clear: () => { /* empty */ },
  getTotal: () => { /* compute total price */ }
}))
```

---

## 6. Docker Setup & Compose

### docker-compose.yml

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:16-alpine
    container_name: ecommerce-postgres
    environment:
      POSTGRES_DB: ecommerce_dev
      POSTGRES_USER: dev_user
      POSTGRES_PASSWORD: dev_pass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./docker/postgres/init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U dev_user -d ecommerce_dev"]
      interval: 5s
      timeout: 5s
      retries: 5

  backend:
    build:
      context: ./backend
      dockerfile: ../docker/backend/Dockerfile
    container_name: ecommerce-backend
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://dev_user:dev_pass@postgres:5432/ecommerce_dev
      JWT_SECRET: change-this-in-production
      STRIPE_SECRET_KEY: sk_test_...
      CORS_ORIGIN: http://localhost:5173
    ports:
      - "3000:3000"
    depends_on:
      postgres:
        condition: healthy
    volumes:
      - ./backend/src:/app/src
      - ./backend/uploads:/app/uploads
    command: npm run dev

  frontend:
    build:
      context: ./frontend
      dockerfile: ../docker/frontend/Dockerfile
    container_name: ecommerce-frontend
    ports:
      - "5173:5173"
    depends_on:
      - backend
    volumes:
      - ./frontend/src:/app/src
      - /app/node_modules

volumes:
  postgres_data:
```

### Dockerfile — Frontend (Multi-stage)

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production serve with Nginx
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY docker/frontend/nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile — Backend

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist /app/dist
COPY --from=builder /app/package*.json ./
RUN npm ci --only=prod
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

---

## 7. Development Workflow

### Step-by-Step Setup

1. **Clone & initialize**

   ```bash
   git init
   mkdir -p ecommerce-platform/{frontend,backend,docker/postgres}
   ```
2. **Scaffold frontend**

   ```bash
   cd frontend
   npm create vite@latest . -- --template react-ts
   npm install zustand axios react-router-dom @stripe/react-js-v2 @stripe/stripe-js
   npm install -D tailwindcss @types/react @types/node
   npx tailwindcss init
   ```
3. **Scaffold backend**

   ```bash
   cd ../backend
   npm init -y
   npm install express prisma stripe bcrypt jsonwebtoken cors dotenv
   npm install -D typescript ts-node @types/express @types/node @types/bcrypt \
     @types/jest jest supertest prisma
   npx prisma init --datasource-provider postgres
   ```
4. **Install Prisma**

   ```bash
   cd backend && npx prisma init
   # Then define schema.prisma (see Section 3) and run:
   npx prisma db push          # Apply schema to database
   npx prisma generate         # Generate TypeScript types
   ```
5. **Run locally**

   ```bash
   docker compose up -d postgres
   cd backend && npx prisma db push && npm run dev
   cd frontend && npm run dev
   ```

### Development Commands

```bash
# Docker (from project root)
docker compose up                    # Start all services
docker compose down                  # Stop all services
docker compose logs -f backend       # Follow backend logs
docker compose logs -f frontend      # Follow frontend logs

# Backend
npm run dev                          # TypeScript watch mode
npm run build                        # Compile to dist/
npm test                             # Run Jest tests
npx prisma migrate up              # Apply migrations
npx prisma migrate reset         # Reset database (dev only)

# Frontend
npm run dev                          # Vite dev server with HMR
npm run build                        # Production build
npm run lint                         # ESLint check
```

---

## 8. Environment Variables

### Backend `.env.example`

```env
NODE_ENV=development
DATABASE_URL=postgresql://dev_user:dev_pass@localhost:5432/ecommerce_dev
JWT_SECRET=your-secret-key-change-in-production
JWT_EXPIRES_IN=7d
STRIPE_SECRET_KEY=sk_test_...          # Test key for development
STRIPE_WEBHOOK_SECRET=whsec_...        # From Stripe dashboard
CORS_ORIGIN=http://localhost:5173      # Frontend URL during dev
PORT=3000
```

### Frontend `.env.example`

```env
VITE_API_URL=http://localhost:3000/api
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_...   # Test key from Stripe
```

---

## 9. Testing Strategy


| Layer               | Tool                  | What to test                                      |
| --------------------- | ----------------------- | --------------------------------------------------- |
| Backend Unit        | Jest + ts-jest        | Auth service, cart logic, price calculations      |
| Backend Integration | Supertest             | API endpoints (register → login → create order) |
| Frontend Component  | React Testing Library | Cart interactions, form validation, auth guards   |
| E2E                 | Cypress (optional)    | Full checkout flow from product page to payment   |

---

## 10. Documentation Structure

Create these files in the project root as you develop:

```
ecommerce-platform/
├── docs/
│   ├── architecture.md          # System design, data flow diagrams
│   ├── api-reference.md        # Full API endpoint documentation (OpenAPI/Swagger)
│   ├── setup-guide.md          # Step-by-step local setup
│   ├── development-workflow.md # Git workflow, PR process, code conventions
│   ├── deployment.md           # Docker production deployment guide
│   ├── testing.md              # How to run tests, coverage targets
│   └── changelog.md            # Version history
```

---

## 11. Security Checklist

- [ ] bcrypt hashing with salt rounds ≥ 12
- [ ] JWT tokens short-lived (7 days or less), stored in httpOnly cookies
- [ ] CORS configured to whitelist only frontend origin
- [ ] Input validation on all API routes (express-validator or Zod)
- [ ] Rate limiting on auth endpoints (express-rate-limit)
- [ ] Stripe webhook signature verification (never trust raw POST bodies)
- [ ] Environment variables never committed to git (.gitignore + .env.example)
- [ ] HTTPS in production (use Let's Encrypt or cloud provider TLS)
- [ ] SQL injection protection (Prisma ORM handles this, but avoid raw queries)

---

## 12. Feature Roadmap

### Phase 1 — Core (MVP)

- User registration + login with JWT
- Product browsing + search + category filters
- Shopping cart + checkout flow
- Stripe payment integration
- Order creation + history

### Phase 2 — Admin Panel

- Product CRUD dashboard
- Order status management
- Sales overview / basic stats

### Phase 3 — Polish & Scale

- Image upload (S3 or Cloudinary)
- Pagination + infinite scroll on shop page
- Email receipts (Nodemailer + SendGrid)
- Promotions / discount codes
- Reviews and ratings

---

## Next Steps

1. **Start with the database schema** — define `schema.prisma` first, then build the backend around it
2. **Build auth flow** — register → login → protected routes (foundation for everything else)
3. **Product CRUD** — both API + admin panel
4. **Cart + Checkout** — cart state management → Stripe integration → order creation
5. **Admin dashboard** — product management UI, order tracking

Each phase should be tested before moving to the next. The Docker setup lets you iterate quickly — just `docker compose up` and your services restart automatically with hot-reload mounted volumes.
