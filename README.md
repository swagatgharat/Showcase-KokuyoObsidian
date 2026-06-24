# 🌌 Kokuyō (黒曜) — Personal Media Tracker

Kokuyō (黒曜) is an elegant, unified media tracking platform designed to curate and monitor Anime, Manga, Movies, TV Series, Books, and Games. It combines personal collection logging, third-party metadata sync, custom playlists, secure session handling, and interactive friend networks into a single, cohesive interface.

## Links

- **Live Demo**: [https://kokuyo.onrender.com](https://kokuyo.onrender.com)

## Image

<img width="3150" height="2205" alt="Kokuyo" src="https://github.com/user-attachments/assets/95891b59-15a4-41c5-8e9e-6638d1975f8f" />

## 1. Project Overview

**Kokuyō** (Japanese for *Obsidian*) is a centralized hub for tracking your digital life. Whether you're an avid reader, a cinephile, a gamer, or an otaku, Kokuyō brings fragmented tracking into one sleek interface.

### Problem Solved

Traditional media tracking is highly fragmented—users need MAL for anime/manga, Letterboxd or TMDB for movies/TV, Goodreads for books, and Backloggd for games. Kokuyō solves this by offering an all-in-one platform to track progress, scores, reviews, and custom collections across all six media types in a single database and dashboard, with local cataloging and third-party API resolution.

### Target Users

- **Media Enthusiasts**: Track progress, scores, reviews, and custom metadata across six media types.
- **Social Trackers**: Share collections and browse what friends are watching, reading, or playing.
- **Data-Driven Organizers**: Use profile stats, Excel export/import, and detailed catalog metadata.

---

## 2. Core Features

- **Universal Tracking**: Manage Anime, Manga, Movies, Web Series, Books, and Games with progress tracking (episodes, chapters, pages, playtime), status categories (*Watching/Reading/Playing* and *Completed*), and custom playlists (`userCollection`).
- **Excel Archive (Export & Import)**: Bulk-add titles or update progress via multi-sheet `.xlsx` workbooks. Re-import sheets for round-trip synchronization with smart resolution matching.
- **Discovery & Integration**: Per-type tabbed discovery powered by third-party APIs (Jikan for Anime/Manga, TMDB for Movies/TV, Google Books/Open Library for Books, IGDB for Games) and fuzzy matching (`fuzzySearch.js`) for Discover result ranking.
- **Social & Friends System**: Connect via shareable `shortId` invites. View friend libraries and copy media with one-click **Add to Kokuyō** copying catalog metadata.
- **Profile & Account**: View collection stats, manage sessions (concurrent devices), change/recover passwords via email OTP, and export/import data.

---

## 3. Tech Stack

- **Frontend**: [React 19](https://react.dev/) / [React Router 7](https://reactrouter.com/)
- **Runtime / Framework**: [Node.js](https://nodejs.org/) / [Express.js 5](https://expressjs.com/)
- **Database**: [MongoDB](https://www.mongodb.com/) via [Mongoose](https://mongoosejs.com/)
- **Styling**: [Tailwind CSS 4](https://tailwindcss.com/)
- **State Management**: [Zustand](https://zustand-demo.pmnd.rs/)

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
- **Middleware**: Custom handlers for token validation, rate-limiting, error handling, and file processing (`backend/middleware`).
- **Models**: Mongoose schemas representing Users, Catalogs, User-Entries, and Friendships (`backend/models`).
- **Services/Utils**: Core logic for email delivery via Gmail API, Excel importing/resolution, external API queries, and cron-scheduled tasks (`backend/utils`).

### Authentication Flow

1. User registers or logs in with email and password → receives an `accessToken` and a `refreshToken` (HttpOnly, SameSite cookie).
2. Access tokens are held in-memory/app-state; refresh tokens are used to request new access tokens on rotation `/api/auth/refresh`.
3. The server supports up to 5 concurrent sessions per user. Each session stores metadata including browser, OS, device, IP, and last active time.
4. OTP verification (via Gmail API OAuth2) is required for password recovery and password change flows.

### Catalog Pipeline

1. Discover/search actions fetch real-time metadata from external API providers (Jikan, TMDB, Google Books, IGDB, Open Library).
2. Adding media creates or resolves a shared catalog entry in MongoDB, downloading and caching covers to ImageKit for stable, CSP-friendly delivery.
3. Import processor maps Excel rows to catalog entries, prioritizing local MongoDB cache and resolving missing metadata from external APIs using fuzzy matching (`fuzzyMatch.js`).

---

## 5. Folder Structure

```text
Kokuyō/
├── package.json             # Root scripts: install-all, dev, build, start
├── frontend/                # React Vite Frontend
│   ├── public/              # Static public assets (manifest, sitemap, robots)
│   ├── src/
│   │   ├── components/      # Cards, Modals, Skeleton loaders, Navbar
│   │   ├── constants/       # Media tabs & global constants
│   │   ├── hooks/           # Custom React hooks (e.g., useMyCollectionExternalIds)
│   │   ├── pages/           # Page routes (Auth, Friends, Landing, User collections)
│   │   ├── store/           # Zustand stores (Auth, etc.)
│   │   └── utils/           # Fuzzy search, addToCollection, device helpers
│   ├── index.html           # Main HTML template with SEO tags
│   └── vite.config.js       # Vite build configuration
├── backend/                 # Express REST API Server
│   ├── middleware/          # JWT Auth, rate limiting, error handlers
│   ├── models/              # Mongoose schemas (User, Catalog, Friendship)
│   ├── routes/              # Express route definition files
│   ├── utils/               # Cron jobs, Excel imports, Gmail OAuth2, catalogs
│   └── server.js            # Express application entrypoint
└── README.md
```

---

## 6. Environment Variables

### Backend Configuration

Create a `.env` file in the `backend/` directory with the following variables:

```env
# Server Configuration
PORT=5000
NODE_ENV=production

# Database
MONGO_URI=your_mongodb_uri

# Security & Sessions
JWT_ACCESS_SECRET=your_jwt_access_secret
JWT_REFRESH_SECRET=your_jwt_refresh_secret

# Email (Gmail API + OAuth2)
GMAIL_USER=your_email@gmail.com
OAUTH_CLIENT_ID=your_google_client_id
OAUTH_CLIENT_SECRET=your_google_client_secret
OAUTH_REFRESH_TOKEN=your_gmail_oauth_refresh_token

# ImageKit (Media Uploads)
IMAGEKIT_PUBLIC_KEY=your_public_key
IMAGEKIT_PRIVATE_KEY=your_private_key
IMAGEKIT_URL_ENDPOINT=your_url_endpoint

# Third-Party API Keys
TMDB_API_KEY=your_tmdb_api_key
GOOGLE_BOOKS_API_KEY=your_google_books_api_key
IGDB_CLIENT_ID=your_twitch_client_id
IGDB_CLIENT_SECRET=your_twitch_client_secret

# Fallback APIs (Optional)
OPEN_LIBRARY_BASE_URL=https://openlibrary.org
OPEN_LIBRARY_COVERS_URL=https://covers.openlibrary.org
```

### Frontend Configuration

Create a `.env` file in the `frontend/` directory with the following variables:

```env
# API Endpoint URL
VITE_API_URL=http://localhost:5000/api
```

---

## 7. Installation & Setup

### Prerequisites

- Node.js (v18+)
- MongoDB (local instance or MongoDB Atlas)
- ImageKit account
- Third-party API keys (TMDB, Google Books, IGDB, Gmail OAuth2)

### Step-by-Step Setup

1. **Clone the Repository**

   ```bash
   git clone https://github.com/swagatgharat/Kokuyo.git
   cd Kokuyo
   ```

2. **Quick Start (Monorepo dev execution)**

   Run the root installer script to configure all dependencies, then run concurrently:
   ```bash
   npm run install-all
   # Create backend/.env and frontend/.env as per Section 6
   npm run dev
   ```

3. **Backend Setup**

   ```bash
   cd backend
   npm install
   # Create .env and fill in variables
   npm run dev
   ```

4. **Frontend Setup**

   ```bash
   cd ../frontend
   npm install
   # Create .env and add VITE_API_URL
   npm run dev
   ```

5. **Production Build**

   ```bash
   npm run build    # Compiles frontend React app to backend-accessible static files
   npm start        # Launches concurrent monolith production mode
   ```

---

## 8. Security Implementation

- **Authentication**: Short-lived JWT access tokens stored in application state; secure, `HttpOnly`, `SameSite=Lax` refresh cookies.
- **Authorization**: Route-level access controlled via custom authentication middleware on the Express server.
- **Session Management**: Session count cap (up to 5 concurrent sessions per account) with detailed browser, OS, and location tracking.
- **OTP Protection**: Email-based OTP verify/request locks and validation attempt limits for password adjustments.
- **Rate Throttling**: Granular rate limit groups applied to authentication routes, verification endpoints, and generic endpoints.
- **Content Security Policy**: Custom Helmet configuration explicitly whitelisting trusted API sources and cover image CDNs.

---

## 9. API Documentation

### Authentication (`/api/auth`)

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/register` | Create a new user account | No |
| `POST` | `/api/auth/login` | Authenticate credentials & initialize session cookie | No |
| `POST` | `/api/auth/refresh` | Rotate and issue a fresh access token | No |
| `POST` | `/api/auth/logout` | Terminate and invalidate current session | Yes |
| `GET` | `/api/auth/me` | Fetch detailed profile data for active session | Yes |
| `POST` | `/api/auth/forgot-password` | Generate & send password recovery OTP | No |
| `POST` | `/api/auth/reset-password` | Validate OTP & update account password | No |
| `POST` | `/api/auth/password/otp` | Request OTP verification for active password update | Yes |
| `POST` | `/api/auth/password/reset` | Update password with existing credentials + active OTP | Yes |
| `GET` | `/api/auth/sessions` | List metadata of all active user sessions | Yes |

### Friends & Network (`/api/friends` & `/api/users`)

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `PATCH` | `/api/users/profile` | Modify active profile display name | Yes |
| `GET` | `/api/users/search` | Search for other users using their short ID | Yes |
| `GET` | `/api/users/:userId/collections` | Fetch friend's library collection | Yes |
| `GET` | `/api/friends` | Retrieve accepted friend list | Yes |
| `GET` | `/api/friends/requests` | Fetch pending incoming request profiles | Yes |
| `POST` | `/api/friends/request/:userId` | Send connection request to another user | Yes |
| `PATCH` | `/api/friends/:requestId/accept` | Accept pending friend request | Yes |
| `DELETE` | `/api/friends/:requestId` | Decline request or unfriend connection | Yes |

### Media Management (`/api/{type}` where `{type}` is `anime`, `manga`, `movies`, `series`, `books`, `games`)

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/{type}/search` | Find matching items in local catalog and external APIs | Yes |
| `POST` | `/api/{type}/add` | Add metadata entry to personal list | Yes |
| `GET` | `/api/{type}/mylist` | Retrieve personal media collection entries | Yes |
| `PATCH` | `/api/{type}/:id` | Update progress, status, score, or comments on entry | Yes |
| `DELETE` | `/api/{type}/:id` | Remove media item from personal list | Yes |
| `GET` | `/api/books/cover` | Proxy book cover CDN requests to avoid CSP redirects | Yes |

### Import & Utilities (`/api/import` & others)

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/import/estimate` | Estimate processing time based on Excel sheet row counts | Yes |
| `POST` | `/api/import/lookup` | Match and process a single imported row against catalog | Yes |
| `GET` | `/api/health` | Backend system status healthcheck | No |
| `POST` | `/api/imagekit/upload` | Direct cloud upload endpoint for custom media covers | Yes |

---

## 10. Deployment Instructions

### Production Monolith Mode

Kokuyō supports a unified production environment where the Node/Express backend serves the pre-compiled frontend code static directory.

1.  **Build Frontend Asset Target**:
    ```bash
    npm run build
    ```
    This script compiles the React SPA to the `frontend/dist` directory.
2.  **Start Production Server**:
    ```bash
    npm start
    ```
    Launches the backend server to concurrently handle API requests and serve frontend SPA fallback routes.
3.  **Host Execution Environment**:
    Ensure the production environment has all standard environment variables listed in Section 6 configured.
    *Gmail OAuth2 is recommended for Render deployments as outbound SMTP connections (such as ports 465/587) are restricted.*

### Split Client/Server Deployment

- **Backend REST API**: Deploy the `/backend` directory to a cloud runtime (e.g. Render, Railway) with backend environment variables.
- **Frontend SPA Client**: Build the client (`npm run build` inside `/frontend`) and host the output `dist` folder on static hosts (e.g. Vercel, Netlify). Set the `VITE_API_URL` to point to the backend domain.

---

## 11. Future Improvements

- **External Account Sync**: Implement MAL / Goodreads OAuth endpoints to import metadata directly.
- **Unified Global Search**: Single dashboard query field matching across all six collection categories.
- **Remote Invalidation**: Disconnect specific concurrent browser sessions from the profile management panel.
- **Real-Time Feed**: Socket-powered status activity stream showing friend collection additions.
- **Analytics Visualization**: Interactive charts tracking consumption trends and scoring habits.

---

## 12. Contributing Guidelines

1.  **Fork** the repository.
2.  Create a **Feature Branch** (`git checkout -b feature/AmazingFeature`).
3.  **Commit** your changes (`git commit -m 'Add some AmazingFeature'`).
4.  **Push** to the branch (`git push origin feature/AmazingFeature`).
5.  Open a **Pull Request**.

**Code Style**: Please follow standard Javascript ES6 patterns and camelCase naming conventions for files and variables.

---

_Designed & developed by [Swagat Gharat](https://github.com/swagatgharat)_
