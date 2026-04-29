# Kokuyō (黒曜) - Personal Media Tracker

Kokuyō is a production-ready, comprehensive personal media tracking application designed to help users organize, track, and manage their consumption of various media types including Anime, Manga, Movies, TV Series, and Books.

Built with a modern tech stack and focusing on security and performance, Kokuyō provides a centralized hub for your digital library, ensuring you never lose track of your progress again.

## 🚀 Project Overview

Kokuyō (meaning "Obsidian" in Japanese) is a unified platform for media enthusiasts. It solves the fragmentation problem of using multiple different apps to track different types of media.

### Target Users
- **Media Enthusiasts**: People who consume large amounts of varied media and need a structured way to track progress.
- **Collectors**: Users who want to maintain a digital catalog of their finished and planned media.
- **Privacy-Conscious Users**: Individuals who prefer a personal, secure tracking system over large public social networks.

---

## ✨ Core Features

### 📺 Media Tracking
- **Anime & Manga**: Search and track your progress through the MyAnimeList database (via Jikan API).
- **Movies & Series**: Comprehensive data for films and television shows (via TMDB API).
- **Books**: Track your reading list using the Google Books database.
- **Progress Management**: Update current episodes, chapters, or pages with one click.
- **Status Organization**: Categorize items as *Watching/Reading*, *Completed*, *On Hold*, *Dropped*, or *Plan to Watch/Read*.

### 📂 Collection Management
- **Custom Collections**: Group your media into logical collections.
- **Smart Filtering**: Filter your library by status, genre, or specific collections.
- **Uncategorized View**: Easily identify items that haven't been assigned to a collection yet.

### 🔐 Security & Auth
- **Session-Based Authentication**: Secure login system using opaque session tokens stored in MongoDB.
- **CSRF Protection**: Robust protection against Cross-Site Request Forgery using the `double-csrf` pattern.
- **Secure Cookies**: HTTP-only, Secure, and Partitioned (CHIPS) cookies for session management.
- **Owner-Only Access**: Strict server-side checks to ensure users can only modify their own media entries.

### 🖼️ Image Management
- **ImageKit Integration**: Automatic uploading and optimization of media covers to ImageKit for lightning-fast delivery.
- **Local Fallbacks**: Intelligent handling of missing covers with placeholder support.

---

## 🛠️ Tech Stack

### Frontend
- **React 19**: Modern UI library with the latest features.
- **Tailwind CSS 4**: Next-generation utility-first CSS framework for high-performance styling.
- **Zustand**: Lightweight and fast state management.
- **React Router 7**: Sophisticated client-side routing.
- **Lucide React**: Clean and consistent iconography.
- **Axios**: Promise-based HTTP client with interceptors for CSRF and Auth.

### Backend
- **Node.js & Express 5**: Fast and minimalist web framework.
- **MongoDB & Mongoose**: Flexible NoSQL database and object modeling.
- **Pino**: High-performance structured logging.

### Security & Utilities
- **double-csrf**: Stateless CSRF protection.
- **Helmet**: Essential security headers for Express.
- **BcryptJS**: Secure password hashing.
- **ImageKit SDK**: Server-side image processing and hosting.

---

## 🏗️ Architecture Overview

Kokuyō follows a decoupled Client-Server architecture:

1.  **Authentication Flow**:
    - User logs in; server generates a cryptographically secure session token.
    - Token is stored in the Database and sent to the client via an `httpOnly` cookie.
    - Subsequent requests are validated against the database session.
2.  **Request-Response Lifecycle**:
    - **Client**: Initiates request with CSRF token in headers and session cookie.
    - **Middleware**: Helmet headers applied -> CORS check -> CSRF validation -> Auth check (Session verification).
    - **Controller**: Business logic execution and Third-party API interaction (TMDB, Jikan, etc.).
    - **Database**: Mongoose interacts with MongoDB for persistent storage.
    - **Response**: Standardized JSON response returned to the frontend.

---

## 📂 Folder Structure

```text
Kokuyo/
├── backend/                # Express Server
│   ├── middleware/         # Auth & Security middlewares
│   ├── models/             # Mongoose Schemas (User, Anime, etc.)
│   ├── routes/             # API Endpoints
│   ├── utils/              # Helper functions (Fetch, Logging)
│   ├── server.js           # Entry point
│   └── .env                # Server environment variables
├── frontend/               # Vite + React App
│   ├── public/             # Static assets
│   ├── src/
│   │   ├── components/     # Reusable UI components
│   │   ├── hooks/          # Custom React hooks
│   │   ├── pages/          # Full page views
│   │   ├── store/          # Zustand state stores
│   │   ├── utils/          # API client and formatters
│   │   └── App.jsx         # Main application component
│   └── .env                # Frontend environment variables
└── README.md
```

---

## 🔑 Environment Variables

### Backend (`backend/.env`)
| Variable | Description |
| :--- | :--- |
| `PORT` | Server port (default: 5000) |
| `MONGO_URI` | MongoDB connection string |
| `CSRF_SECRET` | Secret key for CSRF token generation |
| `IMAGEKIT_PUBLIC_KEY` | Public key from ImageKit dashboard |
| `IMAGEKIT_PRIVATE_KEY` | Private key from ImageKit dashboard |
| `IMAGEKIT_URL_ENDPOINT` | Your ImageKit URL endpoint |
| `TMDB_API_KEY` | API Key for The Movie Database |
| `GOOGLE_BOOKS_API_KEY` | API Key for Google Books API |

### Frontend (`frontend/.env`)
| Variable | Description |
| :--- | :--- |
| `VITE_API_URL` | Full URL to the backend API (e.g., http://localhost:5000/api) |

---

## 🛠️ Installation & Setup

### 1. Prerequisites
- Node.js (v18+)
- MongoDB (Atlas or Local)
- API Keys for: [ImageKit](https://imagekit.io/), [TMDB](https://www.themoviedb.org/), [Google Cloud](https://console.cloud.google.com/)

### 2. Quick Start (Root)
The project includes root-level scripts to manage both frontend and backend:

```bash
# Install all dependencies (Frontend & Backend)
npm run install-all

# Run both in development mode concurrently
npm run dev
```

### 3. Manual Setup

#### Backend Setup
```bash
cd backend
npm install
cp .env.example .env
# Fill in your .env variables
npm run dev
```

#### Frontend Setup
```bash
cd frontend
npm install
cp .env.example .env
# Fill in your VITE_API_URL
npm run dev
```

The application will be available at `http://localhost:5173`.


---

## 🛡️ Security Implementation

- **CSRF Protection**: Every state-changing request (POST, PUT, DELETE) requires a valid `x-csrf-token` header, matched against an encrypted cookie.
- **Ownership Verification**: All media operations (`update`, `delete`) verify that the `resource.userId === authenticatedUser.id`.
- **Session Security**: Sessions are invalidated on logout and have a server-side TTL. Cookies are configured with `SameSite=None` (for cross-origin production) or `Strict` (local).
- **Rate Limiting**: Integrated retry logic for third-party APIs (Jikan) to handle external rate limits gracefully.

---

## 📡 API Documentation (Major Routes)

| Method | Route | Purpose |
| :--- | :--- | :--- |
| `POST` | `/api/auth/register` | User registration |
| `POST` | `/api/auth/login` | User login |
| `GET` | `/api/csrf-token` | Fetch a new CSRF token |
| `GET` | `/api/anime/search` | Search anime (Jikan API) |
| `POST` | `/api/anime/add` | Add anime to personal list |
| `PATCH` | `/api/:media/:id/progress` | Update media progress |
| `DELETE` | `/api/:media/:id` | Remove media from list |

---

## 🚀 Deployment

### Backend (Render/Heroku/Vercel)
1. Set `NODE_ENV=production`.
2. Ensure `FRONTEND_URL` is set to your deployed frontend domain.
3. The server is configured to serve the `frontend/dist` folder automatically if `NODE_ENV` is production.

### Frontend
1. Run `npm run build` in the frontend directory.
2. The resulting `dist` folder should be accessible to the backend server.

---

## 🔮 Future Improvements
- **Social Features**: Allow users to share their public collections.
- **Recommendations**: AI-powered suggestions based on current watch list.
- **Notifications**: Reminders for new episode releases.
- **Mobile App**: PWA or React Native companion app.

---

## 🤝 Contributing
1. Fork the Project.
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`).
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the Branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

*Designed & developed [Swagat Gharat](https://github.com/swagatgharat)*
