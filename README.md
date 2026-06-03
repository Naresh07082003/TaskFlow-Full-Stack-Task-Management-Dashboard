# TaskFlow — Project Management Dashboard

A full-stack web application for task and project management, built with a clean REST API backend and a responsive single-page frontend.

---

## Features

- **User authentication** (JWT-based login/logout)
- **Project & task management** — create, assign, update, delete
- **Role-based access** — Admin, Manager, Developer
- **REST API** with clear endpoint contracts
- **Filtering & search** — by status, priority, assignee
- **Audit logging** — every action is timestamped and logged
- **Responsive UI** — works on desktop and mobile

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                     Frontend (SPA)                  │
│          HTML + CSS + Vanilla JS (ES Modules)        │
│   ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │
│   │  Auth UI │  │Dashboard │  │  Task Detail View │ │
│   └──────────┘  └──────────┘  └──────────────────┘ │
└────────────────────────┬────────────────────────────┘
                         │ REST (JSON)
┌────────────────────────▼────────────────────────────┐
│                 Backend (Node.js + Express)          │
│  ┌───────────┐  ┌──────────────┐  ┌──────────────┐ │
│  │ Auth MW   │  │ Route Layer  │  │ Logger MW    │ │
│  └───────────┘  └──────┬───────┘  └──────────────┘ │
│                        │                             │
│  ┌─────────────────────▼───────────────────────┐   │
│  │              Data Access Layer               │   │
│  │     (JSON file store / swap for DB)          │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer     | Technology                |
|-----------|--------------------------|
| Frontend  | HTML5, CSS3, Vanilla JS  |
| Backend   | Node.js, Express 4       |
| Auth      | JWT (jsonwebtoken)       |
| Storage   | JSON flat-file (swappable to PostgreSQL / MongoDB) |
| Logging   | Custom middleware (structured JSON logs) |

---

## REST API Reference

### Authentication

| Method | Endpoint       | Description         | Auth |
|--------|----------------|---------------------|------|
| POST   | /api/auth/login | Login, get JWT      | No   |
| POST   | /api/auth/logout | Invalidate token   | Yes  |

### Projects

| Method | Endpoint              | Description            | Role     |
|--------|-----------------------|------------------------|----------|
| GET    | /api/projects         | List all projects      | All      |
| POST   | /api/projects         | Create project         | Admin    |
| GET    | /api/projects/:id     | Get project details    | All      |
| PUT    | /api/projects/:id     | Update project         | Manager+ |
| DELETE | /api/projects/:id     | Delete project         | Admin    |

### Tasks

| Method | Endpoint                        | Description           | Role     |
|--------|---------------------------------|-----------------------|----------|
| GET    | /api/projects/:id/tasks         | List tasks            | All      |
| POST   | /api/projects/:id/tasks         | Create task           | Manager+ |
| PUT    | /api/tasks/:id                  | Update task           | Assignee |
| DELETE | /api/tasks/:id                  | Delete task           | Manager+ |

### Users

| Method | Endpoint       | Description         | Role  |
|--------|----------------|---------------------|-------|
| GET    | /api/users     | List users          | Admin |
| GET    | /api/users/me  | Current user profile | All  |

---

## Project Structure

```
taskflow/
├── README.md
├── .gitignore
│
├── backend/
│   ├── server.js              # Entry point, Express setup
│   ├── middleware/
│   │   ├── auth.js            # JWT verification middleware
│   │   └── logger.js          # Request/response logging
│   ├── routes/
│   │   ├── auth.js            # POST /api/auth/*
│   │   ├── projects.js        # CRUD /api/projects
│   │   ├── tasks.js           # CRUD /api/tasks
│   │   └── users.js           # GET  /api/users
│   └── data/
│       ├── users.json         # User store
│       ├── projects.json      # Project store
│       └── tasks.json         # Task store
│
└── frontend/
    ├── index.html             # App shell
    ├── style.css              # Design system + components
    └── app.js                 # SPA router + API client
```

---

## Getting Started

### Prerequisites

- Node.js v18+
- npm v9+

### Installation

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/taskflow.git
cd taskflow

# Install backend dependencies
cd backend
npm install

# Start the API server
node server.js
# → API running on http://localhost:3000
```

### Run the Frontend

Open `frontend/index.html` in your browser, or serve it with any static server:

```bash
# Option A: Python
cd frontend && python3 -m http.server 8080

# Option B: npx
npx serve frontend
```

Then visit `http://localhost:8080`

### Demo Credentials

| Role    | Email                | Password   |
|---------|----------------------|------------|
| Admin   | admin@taskflow.dev   | admin123   |
| Manager | manager@taskflow.dev | manager123 |
| Dev     | dev@taskflow.dev     | dev123     |

---

## Design Decisions

- **Flat-file JSON store** — makes the project self-contained and easy to evaluate. The data access layer (`routes/*.js`) is isolated so swapping to PostgreSQL or MongoDB requires only changing those files.
- **JWT authentication** — stateless, scalable, and industry-standard. Tokens expire after 8 hours.
- **Role-based middleware** — the `auth.js` middleware exposes a `requireRole(...roles)` factory, keeping route definitions clean.
- **Structured logging** — every request logs method, path, status, duration, and user. Easy to pipe into any log aggregator.
- **ES Modules on the frontend** — no bundler needed; the browser handles imports natively.

---

## Extending the Project

| Goal | What to change |
|------|----------------|
| Add a real database | Replace `data/*.json` reads/writes with DB queries in `routes/` |
| Add WebSocket live updates | Add `socket.io` to `server.js` and emit events on task changes |
| Add email notifications | Call a mail service (e.g. Nodemailer) in the task update route |
| Deploy | Add a `Dockerfile` — backend is stateless, frontend is static |

---

## License

MIT
