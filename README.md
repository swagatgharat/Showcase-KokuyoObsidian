# Kokuyo Obsidian (黒曜) - Personal Media Tracker

Kokuyo Obsidian (黒曜) is an elegant, unified media tracking platform designed to curate and monitor Anime, Manga, Movies, TV Series, Books, and Games. It combines personal collection logging, third-party metadata sync, custom playlists, linked progress steppers, secure session handling, dynamic billing cycles, and interactive friend networks into a single, cohesive interface.

## Links

- **Live Demo**: [https://kokuyoobsidian.onrender.com](https://kokuyoobsidian.onrender.com/)

## Image

<img width="3150" height="2205" alt="Kokuyo Obsidian" src="https://github.com/user-attachments/assets/95891b59-15a4-41c5-8e9e-6638d1975f8f" />

---

## 1. Project Overview

**Kokuyo Obsidian** (Japanese for *Obsidian*) is a centralized hub for tracking your digital life. Whether you're an avid reader, a cinephile, a gamer, or an otaku, Kokuyo Obsidian brings fragmented tracking into one sleek interface.

### Problem Solved

Traditional media tracking is highly fragmented—users need MAL for anime/manga, Letterboxd or TMDB for movies/TV, Goodreads for books, and Backloggd for games. Kokuyo Obsidian solves this by offering an all-in-one platform to track progress, scores, reviews, and custom collections across all six media types in a single database and dashboard, with local cataloging and third-party API resolution.

### Target Users

- **Media Enthusiasts**: Track progress, scores, reviews, and custom metadata across six media types.
- **Social Trackers**: Share collections and browse what friends are watching, reading, or playing.
- **Data-Driven Organizers**: Use profile stats, Excel export/import, and detailed catalog metadata.

---

## 2. Core Features

- **Universal Tracking & Linked Progress Steppers**: Manage Anime, Manga, Movies, TV Series, Books, and Games with progress tracking. Features **Bi-directional Linked Progress Steppers** for TV Series (*Season <-> Episode*) and Manga (*Volume <-> Chapter*) that auto-sync progress and status (*Watching/Reading/Playing* vs. *Completed*).
- **Watch/Read Search Shortcuts**: Premium hover overlays on collection cards (Play, Book, or Gamepad icon) that route directly to configured official streaming, reading, or search options. If no platform is matched or configured, searches fallback to Google.
- **Dynamic Cover Refetch & Missing Filter**: Keep your library visually pristine. Sync buttons on missing or placeholder card covers trigger background metadata resolution to fetch fresh covers. A dynamic "Missing" filter pill is automatically added to collections to isolate items requiring cover corrections.
- **Excel Archive (Export & Import)**: Bulk-add titles or update progress via multi-sheet `.xlsx` workbooks. Re-import sheets for round-trip synchronization with smart resolution matching.
- **Discovery & Integration**: Per-type tabbed discovery powered by third-party APIs (Jikan for Anime/Manga, TMDB for Movies/TV, Google Books/Open Library for Books, IGDB for Games) and fuzzy matching (`fuzzySearch.js`) for Discover result ranking.
- **Social & Friends System**: Connect via shareable `platformId` invites. View friend libraries and copy media with one-click **Add to Kokuyo Obsidian** copying catalog metadata.
- **Profile & Account**: View collection stats, manage sessions (concurrent devices), change/recover passwords via email OTP, and export/import data.
- **Session Management & Automatic Revocation**: Security dashboard under the user profile that displays all concurrent active sessions (browser, OS, device, IP, last active time, current session marker) with manual logout options. **Automatic Session Revocation** immediately invalidates all other active sessions when an account password is updated or reset via OTP.
- **Keep-Alive & OAuth Maintenance Cron**: Built-in background interval processes that ping the server to prevent cold starts on free hosting tiers, log server health to an optional dedicated database, and run a 90-day Gmail OAuth2 keep-alive check to prevent credentials from expiring.
- **Multi-Tier Subscription System**: Integrated Dodo Payments gateway supporting four levels: Rookie (Free), Enthusiast, Nerd, and Otaku plans. Supports flexible billing cycles (**Monthly**, **Quarterly**, **Half-Yearly**, **Yearly**) with auto-calculated or admin-configured discount badges, as well as a ₹10 free trial (expires in 7 days). Each tier enforces unique limits for catalog items, friend connections, and concurrent active sessions, along with access to premium features (Excel backups, mutual collection views).
- **Admin Control Panel**: Comprehensive administrative dashboard featuring analytic stats (user growth, revenue metrics, plan distributions, database stats), paginated user list with full profile inspection, manual subscription overrides (plan, billing cycle, and custom expiry adjustments), support for resetting trial eligibility, transactional payment history logs, direct pricing/limits configuration changes with automatic in-memory cache invalidation, catalog item editing, and dynamic redirection platform management.
- **Community Reviews & Recommendations**: Public reviews feed allowing users to rate and write reviews on catalog items, along with a recommendation feed (exclusive to premium tiers) allowing users to recommend catalog media directly from their tracking lists to the community.
- **Legal Consent & Version Control**: Integrated Terms of Service and Privacy Policy consent system. Warns users when policies are updated using a legal update notifier, requiring acceptance of the new versions. Features an in-place markdown editor for administrators to deploy and activate legal document versions directly from the UI.
- **User Banning & Security Controls**: Admin operations allowing temporary or permanent account bans with customization for ban duration, reasons, and appeal deadlines. Automatically logs out banned sessions and notifies users via custom formatted HTML emails.
- **Puppeteer Secure Receipt Service**: Automates custom-branded invoice/receipt generation and delivery via PDF attachment in emails. Includes automated attachment generation for manual UPI transfers (purged from cloud storage after 7 days for user financial privacy).
- **Dual-Mode Background Queues (BullMQ)**: Managed background jobs for cover refetching and streaming/reading availability resolution via BullMQ/Redis with automated, memory-bounded fallback queues when Redis is offline.

---

## 3. Tech Stack

- **Frontend**: [React 19](https://react.dev/) / [React Router 7](https://reactrouter.com/) / [Zustand 5](https://github.com/pmndrs/zustand)
- **Styling**: [Tailwind CSS 4](https://tailwindcss.com/) / [Lucide React](https://lucide.dev/)
- **Backend / Runtime**: [Node.js](https://nodejs.org/) / [Express.js 5](https://expressjs.com/)
- **Database**: [MongoDB](https://www.mongodb.com/) via [Mongoose 9](https://mongoosejs.com/)
- **DevOps & Infrastructure**: [Docker](https://www.docker.com/) / [Docker Compose](https://docs.docker.com/compose/) / [Nginx (Alpine)](https://nginx.org/)
- **Payments / Integrations**: [Dodo Payments API](https://dodopayments.com/) / [Google APIs (Gmail OAuth2)](https://developers.google.com/gmail/api) / [ImageKit SDK](https://imagekit.io/)
- **Utilities**: [XLSX (SheetJS)](https://sheetjs.com/) (Excel import/export) / [Pino](https://github.com/pinojs/pino) (Structured logging) / [ua-parser-js](https://github.com/faisalman/ua-parser-js) (Session metadata) / [Helmet](https://helmetjs.github.io/) (Security headers) / [BullMQ](https://github.com/taskforcesh/bullmq) (Background Jobs) / [Redis](https://redis.io/) (Caching/Job Queue) / [Puppeteer](https://pptr.dev/) (PDF generation) / [Sharp](https://github.com/lovell/sharp) (Image optimization)

---

## 4. Architecture Overview

### Frontend Structure

The frontend is a React Single Page Application (SPA) built using Vite:
- **Routes**: Client-side routing is handled by React Router 7 (`src/pages`).
- **Components**: Reusable UI components including cards, modals, skeletons, and layouts (`src/components`).
- **State Management**: Global and authentication states managed via Zustand (`src/store`).
- **API Layer**: Centralized Axios instance with automatic authentication header injection.
- **Utils**: Helpers for fuzzy search, adding to collections, toast notifications, and device metadata (`src/utils`).

### Backend Structure

The backend implements a modular MVC-like API architecture:
- **Routes**: Defines endpoints and attaches validation and authentication middleware (`backend/routes`).
- **Middleware**: Custom handlers for token validation, rate-limiting, subscription checks, admin checks, error handling, and file processing (`backend/middleware`).
- **Models**: Mongoose schemas representing Users, Subscriptions, PaymentRecords, TierConfigs, Catalogs, User-Entries, and Friendships (`backend/models`).
- **Services/Utils**: Core logic for email delivery via Gmail API, Excel importing/resolution, external API queries, database seeding, tier caching, and cron-scheduled tasks (`backend/utils`).

### Authentication & Session Security Flow

1. User registers or logs in with email and password → receives an `accessToken` and a `refreshToken` (HttpOnly, SameSite cookie).
2. Access tokens are held in-memory/app-state; refresh tokens are used to request new access tokens on rotation `/api/auth/refresh`.
3. The server enforces a dynamic session cap based on the subscription tier (Rookie/Trial: 2, Enthusiast: 3, Nerd: 5, Otaku: 10 concurrent active sessions). Each session stores metadata including browser, OS, device, IP, and last active time.
4. **Automatic Session Revocation**: Updating or resetting an account password automatically wipes all other active refresh tokens from the database, immediately terminating unauthorized device sessions.

### Subscription Limits & Locking Pipeline

1. **Gatekeeping**: Adding a new catalog item or friend connection checks the active subscription limits using `canAddItem` and `canAddFriend`. Exceeding these returns a `403 Forbidden` error with a `limitReached` flag.
2. **Locking Mechanism**: If a user's subscription expires or is manually downgraded, their existing records exceeding the plan caps are not deleted. Instead, they are marked as `locked: true` based on an oldest-first order (sorted ascending by `createdAt`).
3. **Restricted Access**: Locked items and friends are read-only in the UI. Progress updates, status changes, and social views (e.g. mutual friends list) are disabled for locked items/friends.
4. **Validation**: Subscriptions are evaluated lazily on read/write actions using the `resolveSubscription` middleware to instantly handle natural expiries and trial expiration. A daily cron reconciliation sweep also runs to handle expiries and send 7-day warnings.

### Admin Control & Config Pipeline

1. **Auth Controls**: Routes under `/api/admin` are secured by combining JWT protection and the `requireAdmin` middleware.
2. **Pricing & Limits Cache**: Subscription tier configs are cached in-memory at startup (`initializeTiersCache`) to avoid repetitive DB lookups.
3. **Instant Sync**: When an admin updates a tier configuration (caps, pricing, feature flags) via `PUT /api/admin/tiers/:plan`, the cache is invalidated (`invalidateTiersCache`) so updates immediately take effect app-wide.
4. **Manual Overrides**: Administrators can override any user's subscription tier, billing cycle (**Monthly**, **Quarterly**, **Half-Yearly**, **Yearly**, **Custom**), and expiry date with audit log tracking.

---

## 5. Folder Structure

```text
Kokuyo Obsidian/
├── package.json             # Root scripts: dev, build, start
├── pnpm-workspace.yaml      # pnpm workspace configuration
├── pnpm-lock.yaml           # pnpm lockfile
├── frontend/                # React Vite Frontend
│   ├── public/              # Static public assets (manifest, sitemap, robots)
│   ├── src/
│   │   ├── api/             # Centralized Axios API setup and interceptors
│   │   ├── components/      # Cards, Modals, Skeleton loaders, Navbar, ProtectedRoute, AdminRoute
│   │   ├── constants/       # Media tabs & global constants
│   │   ├── hooks/           # Custom React hooks
│   │   ├── pages/           # Page routes
│   │   │   ├── admin/       # Admin Dashboard, Catalog, Payments, Pricing, Platform configuration, System Logs viewer, Users list & details
│   │   │   ├── auth/        # Login, Register, Forgot Password
│   │   │   ├── common/      # Landing page, Pricing list, Contact form, Terms & Privacy (with admin edit mode)
│   │   │   └── users/       # Profile/Security dashboard, Search catalog, Community Recommendations
│   │   │       ├── collection/ # MyCollection layout wrapper & section components (Anime, Book, Game, etc.)
│   │   │       └── friends/ # Friends List, Friend requests management, and Friend collections
│   │   ├── store/           # Zustand stores (Auth, Toast, etc.)
│   │   ├── utils/           # Fuzzy search, addToCollection, device helpers
│   │   ├── App.jsx          # SPA entry layout & client route definitions
│   │   ├── main.jsx         # React DOM entrypoint
│   │   └── index.css        # Global CSS & Tailwind styling setup
│   ├── index.html           # Main HTML template with SEO tags
│   └── vite.config.js       # Vite build configuration
├── backend/                 # Express REST API Server
│   ├── config/              # Default configurations (Redis connection, Dodo Payments, Tiers layout)
│   ├── constants/           # Core constants & seed defaults (legal policies, platforms, tier defaults)
│   ├── controllers/         # Routing handlers for each entity
│   ├── middleware/          # JWT Auth, rate limiting, subscription/admin checkers, error handlers
│   ├── models/              # Mongoose schemas
│   ├── routes/              # Express API route definition files
│   ├── scripts/             # Database seeds and maintenance tasks
│   ├── services/            # Async queues and processors (BullMQ, Puppeteer, email)
│   ├── utils/               # Helper modules (cronLogger, sitemap generator, fuzzyMatch, tierCache)
│   └── server.js            # Express application entrypoint
└── README.md
```

---

## 6. Environment Variables

### Backend Configuration (`backend/.env`)

```env
# Server Configuration
PORT=5000
NODE_ENV=production
FRONTEND_URL=https://kokuyo.onrender.com
BACKEND_URL=https://kokuyo.onrender.com

# Database Configuration
MONGO_URI=your_mongodb_cluster_uri
LOG_MONGO_URI=your_log_mongodb_cluster_uri  # Optional separate log DB

# Keep-Alive Configuration
PING_TARGET_URL=https://kokuyo.onrender.com/api/health

# Security & Sessions (JWT)
JWT_ACCESS_SECRET=your_secure_jwt_access_secret
JWT_REFRESH_SECRET=your_secure_jwt_refresh_secret

# Official Email (Gmail API + OAuth2)
GMAIL_USER=kokuyoobsidianofficial@gmail.com
OAUTH_CLIENT_ID=your_google_oauth_client_id
OAUTH_CLIENT_SECRET=your_google_oauth_client_secret
OAUTH_REFRESH_TOKEN=your_google_oauth_refresh_token

# ImageKit Configuration (Image Hosting)
IMAGEKIT_PUBLIC_KEY=your_imagekit_public_key
IMAGEKIT_PRIVATE_KEY=your_imagekit_private_key
IMAGEKIT_URL_ENDPOINT=https://ik.imagekit.io/your_imagekit_id

# Third-Party Media APIs
TMDB_API_KEY=your_tmdb_api_key
TMDB_BASE_URL=https://api.themoviedb.org/3
TMDB_IMAGE_BASE=https://image.tmdb.org/t/p/w500

GOOGLE_BOOKS_API_KEY=your_google_books_api_key
GOOGLE_BOOKS_BASE_URL=https://www.googleapis.com/books/v1

OPEN_LIBRARY_BASE_URL=https://openlibrary.org
OPEN_LIBRARY_COVERS_URL=https://covers.openlibrary.org

JIKAN_BASE_URL=https://api.jikan.moe/v4

IGDB_CLIENT_ID=your_twitch_igdb_client_id
IGDB_CLIENT_SECRET=your_twitch_igdb_client_secret

# Dodo Payments Gateway
DODO_PAYMENTS_API_KEY=your_dodo_payments_api_key
DODO_PAYMENTS_WEBHOOK_SECRET=your_dodo_payments_webhook_secret
DODO_PAYMENTS_ENVIRONMENT=live_mode

# Admin Credentials (GMAIL_USER is used as default admin email)
ADMIN_PASSWORD=your_secure_admin_password
```

---

## 7. Installation & Setup

### Option A: Running with Docker (Recommended for Containerized Setup)

Ensure [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) are installed on your machine.

1. **Clone the Repository**
   ```bash
   git clone https://github.com/swagatgharat/Kokuyo_Obsidian.git
   cd Kokuyo_Obsidian
   ```

2. **Configure Environment Variables**
   Create `backend/.env` as detailed in [Section 6](#6-environment-variables).

3. **Start the Entire Stack (Backend + Frontend Nginx + Redis)**
   ```bash
   docker compose up --build -d
   ```
   - **Frontend Application**: `http://localhost:80`
   - **Backend API**: `http://localhost:5000/api`
   - **Health Check**: `http://localhost:5000/api/health`

4. **Stop the Containers**
   ```bash
   docker compose down
   ```

*(See [DOCKER_GUIDE.md](DOCKER_GUIDE.md) for advanced commands, single-service builds, and production container tips).*

---

### Option B: Native Local Setup

#### Prerequisites
- Node.js (v18+)
- pnpm (v8+)
- MongoDB Atlas cluster
- ImageKit account
- Google Cloud OAuth2 credentials (*Publishing status: In production*)

#### Step-by-Step Setup
1. **Clone the Repository**
   ```bash
   git clone https://github.com/swagatgharat/Kokuyo_Obsidian.git
   cd Kokuyo_Obsidian
   ```

2. **Install Workspace Dependencies**
   ```bash
   pnpm install
   ```

3. **Configure Environment Files**
   Create `backend/.env` as detailed in [Section 6](#6-environment-variables).

4. **Development Launch**
   ```bash
   pnpm dev      # Runs both backend and frontend concurrently with hot-reload
   ```

5. **Production Build & Launch**
   ```bash
   pnpm build    # Compiles React frontend SPA into dist
   pnpm start    # Launches production backend API server
   ```

---

## 8. Deployment Architecture (Render / Docker / Cloud VPS)

- **Docker Container Deployment (Any Cloud / VPS)**:
  - Backend Dockerfile: `backend/Dockerfile`
  - Frontend Dockerfile: `frontend/Dockerfile` (served with production-tuned Nginx)
  - Stack Orchestration: `docker-compose.yml`

- **Unified Monolith Deployment on Render**:
  - Root Build Command: `pnpm install && pnpm build`
  - Root Start Command: `pnpm start`
  - Environment: Set `NODE_ENV=production`, `FRONTEND_URL=https://your-domain.com`, `GMAIL_USER=kokuyoobsidianofficial@gmail.com`.

- **Gmail OAuth2 in Production**:
  - In Google Cloud Console, set your OAuth consent screen **Publishing Status to "In production"**.
  - Refresh tokens generated in Production status remain permanent and do not expire after 7 days.

---

## 9. Security Implementation

- **Authentication**: Short-lived JWT access tokens stored in application state; secure, `HttpOnly`, `SameSite=Lax` refresh cookies.
- **Automatic Session Invalidation**: Password updates immediately purge all other active refresh tokens from the database.
- **OTP Fail Guard**: Database-backed attempt counters block OTP brute-forcing across rotating IPs.
- **Granular Rate Throttling**: Protects login, registration, password resets, and support submissions against abuse.

---

_Designed & developed by [Swagat Gharat](https://github.com/swagatgharat)_
