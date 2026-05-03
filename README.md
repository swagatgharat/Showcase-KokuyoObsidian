# 🌌 Kokuyō (黒曜) — Personal Media Tracker

Kokuyō is a sophisticated, all-in-one media tracking platform designed for enthusiasts to curate and monitor their consumption of Anime, Manga, Movies, TV Series, and Books. Built with a focus on aesthetics and security, it provides a seamless experience for organizing personal collections and connecting with friends.

---

## 🚀 Project Overview

**Kokuyō** (Japanese for *Obsidian*) is a centralized hub for tracking your digital life. Whether you're an avid reader, a cinephile, or an otaku, Kokuyō solves the problem of fragmented tracking across multiple platforms by bringing everything into one sleek, unified interface.

### Target Users
- **Media Enthusiasts**: Users who want to track their progress, scores, and reviews for various media types.
- **Social Trackers**: Users who want to share their collections and see what their friends are watching or reading.
- **Data-Driven Organizers**: Users who appreciate detailed statistics and metadata for their personal library.

---

## ✨ Core Features

### 🎞️ Media Management
- **Universal Tracking**: Manage Anime, Manga, Movies, TV Series, and Books.
- **Progress Monitoring**: Update episodes watched, chapters read, or pages finished.
- **Status Categories**: Organize items into *Watching/Reading*, *Completed*, *Plan to Watch/Read*, *On Hold*, and *Dropped*.
- **Detailed Metadata**: Add personal scores, tags, reviews, and custom cover images.

### 🔍 Discovery & Integration
- **Smart Search**: Integrated with third-party APIs for instant metadata fetching:
  - **Jikan API**: For Anime and Manga.
  - **TMDB API**: For Movies and TV Series.
  - **Google Books API**: For Books.
- **Auto-Fill**: Automatically fetch titles, synopses, and default covers.

### 👥 Social & Friends System
- **Friend Requests**: Send and receive friend requests to build your network.
- **Member Directory**: Discover other users on the platform.
- **Profile Cards**: Quick view of user stats and collection highlights.
- **Shared Collections**: Quick access to friend's media libraries.

### 🛡️ Security & Performance
- **Secure Authentication**: OTP-based registration and login system for enhanced security.
- **State Management**: Fluid UI transitions and efficient data handling using Zustand.
- **Rate Limiting**: Protection against brute-force and spam on sensitive endpoints.
- **CSRF Protection**: Robust defense against cross-site request forgery.

---

## 🛠️ Tech Stack

### Frontend
- **Framework**: React 19 (Vite)
- **Styling**: Tailwind CSS 4
- **Icons**: Lucide React
- **State Management**: Zustand
- **HTTP Client**: Axios

### Backend
- **Runtime**: Node.js
- **Framework**: Express 5
- **Database**: MongoDB (via Mongoose)
- **Logging**: Pino & Pino-pretty
- **Security**: 
  - JWT (JSON Web Tokens) with HttpOnly cookies
  - `csrf-csrf` for CSRF protection
  - `helmet` for HTTP header security
  - `express-rate-limit` for DDoS/Brute-force protection
  - `bcryptjs` for password hashing

### Third-Party Services
- **ImageKit**: Cloud storage for media covers and user images.
- **Nodemailer**: Email delivery for OTP and notifications.

---

## 🏗️ Architecture Overview

Kokuyō follows a decoupled Client-Server architecture:

1.  **Frontend (React)**: A modern SPA (Single Page Application) that communicates with the backend via a RESTful API. It uses **Zustand** for lightweight global state and **React Router** for navigation.
2.  **Backend (Express)**: A robust REST API that handles business logic, authentication, and database interactions.
3.  **Authentication Flow**:
    - User registers/logs in via email.
    - System sends a 6-digit OTP via email.
    - Upon verification, a JWT is issued and stored in an **HttpOnly cookie**.
    - All subsequent requests include the JWT and a **CSRF token** for validation.
4.  **Request-Response Lifecycle**:
    - Requests pass through global middleware (Helmet, CORS, Rate Limiter).
    - Authentication middleware validates the JWT.
    - Route handlers interact with Mongoose models.
    - Responses are returned in a standardized JSON format.

---

## 📂 Folder Structure

```text
Kokuyō/
├── frontend/                # React application
│   ├── public/              # Static assets
│   ├── src/
│   │   ├── components/      # Reusable UI components
│   │   ├── pages/           # Page-level components
│   │   ├── store/           # Zustand state definitions
│   │   ├── utils/           # Helper functions & API config
│   │   └── App.jsx          # Root component & Routing
│   └── vite.config.js
├── backend/                 # Node.js Express API
│   ├── middleware/          # Auth, Rate limiting, CSRF
│   ├── models/              # Mongoose schemas
│   ├── routes/              # API endpoints
│   ├── utils/               # Email templates & Helpers
│   ├── server.js            # Entry point
│   └── .env.example         # Environment template
└── README.md
```

---

## 🔑 Environment Variables

### Backend (`/backend/.env`)
| Variable | Description |
| :--- | :--- |
| `PORT` | Server port (default: 5000) |
| `MONGO_URI` | MongoDB connection string |
| `CSRF_SECRET` | Secret key for CSRF token generation |
| `IMAGEKIT_PUBLIC_KEY` | Public key from ImageKit dashboard |
| `IMAGEKIT_PRIVATE_KEY` | Private key for server-side auth |
| `IMAGEKIT_URL_ENDPOINT` | Your ImageKit URL endpoint |
| `TMDB_API_KEY` | API key for Movie/Series search |
| `GOOGLE_BOOKS_API_KEY` | API key for Book search |

### Frontend (`/frontend/.env`)
| Variable | Description |
| :--- | :--- |
| `VITE_API_URL` | Backend API base URL (e.g., `http://localhost:5000/api`) |

---

## ⚙️ Installation & Setup

### 1. Prerequisites
- Node.js (v18+)
- MongoDB (Local or Atlas)
- ImageKit Account

### 2. Backend Setup
```bash
cd backend
npm install
cp .env.example .env
# Fill in your environment variables in .env
npm run dev
```

### 3. Frontend Setup
```bash
cd frontend
npm install
cp .env.example .env
# Ensure VITE_API_URL points to your backend
npm run dev
```

---

## 🛡️ Security Implementation

- **Role-Based Access**: Users can only modify their own media collections.
- **Ownership Validation**: Backend middleware ensures that `user_id` in requests matches the authenticated user.
- **CSRF Protection**: Every state-changing request (POST, PUT, DELETE) requires a valid X-CSRF-Token.
- **Rate Limiting**: 
  - `auth`: Strict limits on login/OTP attempts (e.g., 5 attempts per hour for OTP).
  - `api`: General limits to prevent abuse.

---

## 📡 API Documentation (Overview)

| Method | Endpoint | Purpose |
| :--- | :--- | :--- |
| `POST` | `/api/auth/register` | Register a new user |
| `POST` | `/api/auth/verify-otp` | Verify OTP and login |
| `GET` | `/api/user/profile` | Get current user data |
| `GET` | `/api/anime` | Fetch user's anime collection |
| `POST` | `/api/anime/add` | Add new anime to tracker |
| `GET` | `/api/friends/list` | View friends list |
| `POST` | `/api/friends/request` | Send a friend request |

---

## 🚀 Deployment

### Backend
1. Use **Render**, **Railway**, or **Heroku**.
2. Set environment variables in the platform's dashboard.
3. Ensure `NODE_ENV` is set to `production`.

### Frontend
1. Build the project: `npm run build`.
2. Deploy the `dist` folder to **Vercel**, **Netlify**, or serve via the backend.

---

## 🔮 Future Improvements
- **Bulk Import**: Import data from MyAnimeList or Goodreads.
- **Global Search**: Search across all media types simultaneously.
- **Activity Feed**: Real-time updates from friends.
- **Statistics Dashboard**: Visual charts for media consumption habits.

---

## 🤝 Contributing
1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/AmazingFeature`).
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

*Designed & developed [Swagat Gharat](https://github.com/swagatgharat)*
