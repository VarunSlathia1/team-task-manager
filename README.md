# TaskFlow — Team Task Manager

A full-stack team task management application with role-based access control, project management, kanban boards, and real-time dashboards.

![TaskFlow](https://img.shields.io/badge/Stack-React%20%2B%20Node%20%2B%20PostgreSQL-amber)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 🚀 Live Demo

- **Live URL**: `https://your-app.railway.app` ← replace after deployment
- **Admin login**: `admin@taskflow.com` / `admin123`
- **Member login**: `member@taskflow.com` / `member123`

---

## ✨ Features

### Authentication & Authorization
- JWT-based signup/login with bcrypt password hashing
- Role-based access: **Admin** (full access) and **Member** (scoped access)
- Project-level roles — per-project Admin/Member distinctions
- Protected routes on both frontend and backend

### Project Management
- Create, edit, delete projects with custom colors
- Add/remove team members per project
- Project-level role assignments

### Task Management
- Create tasks with title, description, status, priority, due date, assignee
- **Kanban board** view with drag-free status changer
- **List/Table** view with inline status updates
- Full CRUD with permission checks
- Comment threads on tasks
- Overdue detection and visual indicators

### Dashboard
- Total tasks, projects, overdue count, completion rate
- Status breakdown with progress bars
- My Upcoming Tasks panel
- Recent activity table

### Team
- View all team members with roles
- Identify yourself in the list

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18, React Router v6, Vite |
| Backend | Node.js, Express.js |
| Database | PostgreSQL (via Prisma ORM) |
| Auth | JWT + bcryptjs |
| Validation | express-validator |
| Deployment | Railway |

---

## 📁 Project Structure

```
team-task-manager/
├── backend/
│   ├── middleware/
│   │   └── auth.js          # JWT + role middleware
│   ├── prisma/
│   │   ├── schema.prisma    # DB schema
│   │   └── seed.js          # Demo data
│   ├── routes/
│   │   ├── auth.js          # /api/auth/*
│   │   ├── projects.js      # /api/projects/*
│   │   ├── tasks.js         # /api/tasks/*
│   │   └── dashboard.js     # /api/dashboard
│   ├── server.js            # Express entry point
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Modal.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   └── utils.jsx    # Badges, Avatar, helpers
│   │   ├── context/
│   │   │   └── AuthContext.jsx
│   │   ├── pages/
│   │   │   ├── Login.jsx
│   │   │   ├── Signup.jsx
│   │   │   ├── Dashboard.jsx
│   │   │   ├── Projects.jsx
│   │   │   ├── ProjectDetail.jsx
│   │   │   ├── Tasks.jsx
│   │   │   └── Team.jsx
│   │   ├── api.js           # API client
│   │   ├── App.jsx          # Routes
│   │   └── index.css        # Design system
│   └── package.json
├── railway.toml
├── nixpacks.toml
└── package.json
```

---

## 🏃 Running Locally

### Prerequisites
- Node.js 18+
- PostgreSQL database

### Setup

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/team-task-manager.git
cd team-task-manager

# 2. Install all dependencies
npm run install:all

# 3. Configure backend environment
cp backend/.env.example backend/.env
# Edit backend/.env with your DATABASE_URL and JWT_SECRET

# 4. Run DB setup and seed
cd backend
npx prisma db push
npm run db:seed
cd ..

# 5. Start both servers (in separate terminals)
npm run dev:backend   # http://localhost:5000
npm run dev:frontend  # http://localhost:5173
```

---

## 🚀 Deploying to Railway

### Step 1 — Push to GitHub
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/team-task-manager.git
git push -u origin main
```

### Step 2 — Create Railway Project
1. Go to [railway.app](https://railway.app) and sign in
2. Click **New Project** → **Deploy from GitHub repo**
3. Select your `team-task-manager` repository

### Step 3 — Add PostgreSQL Database
1. In your Railway project, click **+ New** → **Database** → **PostgreSQL**
2. Railway auto-sets `DATABASE_URL` — copy it for reference

### Step 4 — Set Environment Variables
In your Railway service settings, add:
```
DATABASE_URL    = (auto-set by Railway PostgreSQL plugin)
JWT_SECRET      = your-random-secret-min-32-chars
NODE_ENV        = production
PORT            = 5000
```

> **Note:** On first boot, `prisma db push` automatically creates all tables from the schema. No manual migration needed.

### Step 5 — Run Seed (optional demo data)
In Railway's service shell or via a one-time job:
```bash
cd backend && npm run db:seed
```

### Step 6 — Deploy
Railway auto-deploys on push. Your app will be live at the generated URL.

---

## 📡 REST API Reference

### Auth
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/auth/signup` | ✗ | Register new user |
| POST | `/api/auth/login` | ✗ | Login, returns JWT |
| GET | `/api/auth/me` | ✓ | Get current user |
| GET | `/api/auth/users` | ✓ | List all users |

### Projects
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/projects` | ✓ | List user's projects |
| POST | `/api/projects` | ✓ | Create project |
| GET | `/api/projects/:id` | ✓ Member | Get project + tasks |
| PUT | `/api/projects/:id` | ✓ Admin | Update project |
| DELETE | `/api/projects/:id` | ✓ Admin | Delete project |
| POST | `/api/projects/:id/members` | ✓ Admin | Add member |
| DELETE | `/api/projects/:id/members/:uid` | ✓ Admin | Remove member |

### Tasks
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/tasks` | ✓ | List tasks (filterable) |
| POST | `/api/tasks` | ✓ Member | Create task |
| GET | `/api/tasks/:id` | ✓ | Get task + comments |
| PUT | `/api/tasks/:id` | ✓ Member | Update task |
| PATCH | `/api/tasks/:id/status` | ✓ Member | Quick status update |
| DELETE | `/api/tasks/:id` | ✓ Creator/Admin | Delete task |
| POST | `/api/tasks/:id/comments` | ✓ | Add comment |

#### Task Query Params
```
GET /api/tasks?status=IN_PROGRESS&priority=HIGH&search=bug&overdue=true&projectId=xxx
```

### Dashboard
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/dashboard` | ✓ | Aggregated stats |

---

## 🔒 Role-Based Access Control

| Action | Admin | Project Admin | Member |
|--------|-------|--------------|--------|
| See all projects | ✅ | — | — |
| Create project | ✅ | — | ✅ |
| Edit/Delete project | ✅ | ✅ | ✗ |
| Manage members | ✅ | ✅ | ✗ |
| Create tasks | ✅ | ✅ | ✅ |
| Edit any task | ✅ | ✅ | own only |
| Delete task | ✅ | — | creator |
| Update task status | ✅ | ✅ | ✅ |
| View team | ✅ | ✅ | ✅ |

---

## 🗄️ Database Schema

```
User ──< ProjectMember >── Project ──< Task ──< Comment
         (role: Admin/Member)           ↑
                                     assignee → User
                                     creator  → User
```

Models: `User`, `Project`, `ProjectMember`, `Task`, `Comment`

---

## 📝 License

MIT © 2024
