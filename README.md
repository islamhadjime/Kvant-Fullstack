# Kvant-Management

**Full-stack CMS for a children's technopark («Кванториум»): public website, admin panel, teacher cabinet, programs, news, documents, FAQ, and enrollment applications.**  
Dark/light UI, **Russian interface**, mobile-friendly responsive layout.

**Price: $50** — full project source code (frontend + backend + Docker + Fly.io config).  
**Contact / purchase:** Telegram — [@history_none](https://t.me/history_none)

---
---
![PHOTO — скриншот](https://github.com/islamhadjime/Kvant-Fullstack/blob/main/code/CODE.png)
## Demo video
Watch the project walkthrough on YouTube:

**[YouTube demo — add your link here](https://www.youtube.com/watch?v=-zhhre7_hvE)**


## What you get

- Complete source code (React + Express)
- SQLite database with demo seed (programs, teachers, news, FAQ, documents)
- Admin panel for all content
- Teacher personal cabinet (journal, students, curriculum, documents)
- Production-ready Docker image + `fly.toml` (optional cloud deploy)
- README with local setup instructions

**GitHub (reference):** [islamhadjime/Kvant-Management](https://github.com/islamhadjime/Kvant-Management)

---

## Highlights

| Feature | Description |
|---------|-------------|
| Public site | Home, programs, news, staff, contacts, legal documents (Сведения) |
| Admin panel | Edit header, hero, slider, Gosuslugi widget, services, FAQ, staff, programs, news, applications |
| Teacher cabinet | Profile, curriculum, students, grades journal, documents, notifications |
| Auth | JWT — roles: `admin`, `teacher` |
| Database | SQLite (file-based), easy demo reset |
| i18n UI | Russian labels and content out of the box |
| Responsive | Mobile, tablet, desktop layouts |
| Accessibility | “Version for visually impaired” (font size, contrast, images toggle) |

---

## Tech stack

### Frontend
- **React 18** + **TypeScript**
- **Vite** — dev server & build
- **React Router** — routing
- **Zustand** — state management
- **TanStack Query** — async data
- **Tailwind CSS** + **shadcn/ui** (Radix)
- **Axios** — HTTP client
- **Quill** — rich text in admin
- **React Helmet Async** — SEO meta tags

### Backend
- **Node.js 20** + **Express 4**
- **TypeScript**
- **Sequelize ORM** + **SQLite** (PostgreSQL-ready via env)
- **JWT** + **bcryptjs** — authentication
- **CORS**, JSON body up to 50MB (images in content)

### DevOps
- **Docker** — multi-stage build (frontend + API in one container)
- **fly.toml** — Fly.io deploy with volume for SQLite (optional)

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     Browser (React SPA)                  │
│  Public pages │ Admin │ Teacher │ Auth                   │
└──────────────────────────┬──────────────────────────────┘
                           │ HTTP / REST
                           ▼
┌─────────────────────────────────────────────────────────┐
│              Express API (Node.js)                         │
│  /api/auth │ /api/public │ /api/admin │ /api/teacher     │
│  Legacy GET: /home, /news, /staff, /services, …         │
└──────────────────────────┬──────────────────────────────┘
                           │ Sequelize
                           ▼
┌─────────────────────────────────────────────────────────┐
│              SQLite (kvant.db)                           │
│  users, staff, programs, news, faq, useful_services,     │
│  home_content, svedeniya, applications, …               │
└─────────────────────────────────────────────────────────┘
```

**Production mode:** Express serves the built React app from `/app/public` and API on the same origin (no CORS issues).

**Development mode:** Vite (`:8080`) proxies API requests to Express (`:3000`).

---

## Project structure

```
Kvant-Management/
├── src/                    # React frontend
│   ├── pages/publick/      # Public pages (Index, Program, News, Staff, …)
│   ├── pages/admin/        # Admin panel
│   ├── pages/teacher/      # Teacher cabinet
│   ├── components/         # UI, layout, cards, editors
│   ├── stores/             # Zustand stores (home, news, faq, …)
│   └── utils/api.ts        # Axios clients
├── server/
│   ├── src/
│   │   ├── models/         # Sequelize models
│   │   ├── repositories/   # Data access layer
│   │   ├── routes/         # public / admin / teacher / auth
│   │   ├── db/             # seed, demoData, clearData
│   │   └── middleware/     # JWT, SPA serving, error handler
│   └── data/               # SQLite file (local, gitignored)
├── Dockerfile
├── fly.toml
└── README.md
```

---

## How it works

### Public website
- **Home:** hero with org info, image slider, enrollment form, Gosuslugi widget, programs, news, team, FAQ, useful services
- **Programs:** filter by tags, program detail pages
- **News:** list + detail with rich HTML
- **Staff:** team grid + modal bio
- **Сведения:** educational org documents (sections + PDF links)
- **Contacts:** address, phone, email, bus stops

### Admin panel (`/admin/login`)
- **Основное:** site header, logo, hero, slider, Gosuslugi embed code
- **Полезные услуги:** CRUD with icons and internal/external URLs
- **FAQ:** questions & answers
- **Педагоги:** staff + optional teacher login accounts
- **Программы:** full program editor (curriculum, schedule, students)
- **Новости:** CRUD with images and tags
- **Заявки:** enrollment applications from the home form
- **Документы:** Svedeniya sections (HTML + document lists)

### Teacher cabinet (`/teacher`)
- Linked to staff record via user account
- Profile, curriculum topics, students, advanced grade journal, documents, notifications

### Demo data
On first run (empty DB), the app seeds:
- Director + 3 teachers (IT, Physics, Chemistry)
- 3 programs, 3 news items, FAQ, services, Svedeniya documents
- Default admin: `admin` / `admin123` (change in production)

Reset demo:
```bash
cd server && npm run db:clear
```

---

## API overview

### Auth
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/auth/login` | Login → JWT token |

Header for protected routes: `Authorization: Bearer <token>`

### Public read (also under `/api/public/…`)
| Method | Path | Description |
|--------|------|-------------|
| GET | `/home` | Home content (logo, texts, slider, widget) |
| GET | `/useful-services` | Useful services list |
| GET | `/faq` | FAQ entries |
| GET | `/groups` | Groups |
| GET | `/news`, `/news/:id` | News |
| GET | `/staff`, `/staff/:id` | Team |
| GET | `/services`, `/services/:id` | Programs (alias) |
| GET | `/programs`, `/programs/:id` | Programs |
| GET | `/svedeniya` | Legal / org documents |
| POST | `/applications` | Enrollment form (can disable via env) |
| GET | `/health` | Health check |

### Admin (`/api/admin/*`, role: admin)
CRUD for: home, useful-services, faq, groups, news, staff, programs/services, svedeniya, applications (status update/delete)

### Teacher (`/api/teacher/*`, role: teacher)
Profile, curriculum, students, journal, grades, homework, documents, notifications

---

## Environment variables

| Variable | Description |
|----------|-------------|
| `PORT` | Server port (default `3000`, production `8080`) |
| `JWT_SECRET` | JWT signing secret (**required in production**) |
| `ADMIN_PASSWORD` | Admin password on first DB seed |
| `DATABASE_PATH` | SQLite file path |
| `STATIC_DIR` | Path to Vite build (production) |
| `DISABLE_APPLICATIONS` | `true` — disable public enrollment form |
| `CORS_ORIGIN` | CORS origin (dev: `http://localhost:8080`) |
| `DB_DIALECT` | `sqlite` (default) or `postgres` |
| `DATABASE_URL` | PostgreSQL connection string |

See `server/.env.example`.

---

## Quick start (local)

### Requirements
- Node.js 20+
- npm

### Install & run
```bash
# Frontend
npm install

# Backend
cd server && npm install && cd ..

# Run both (from project root)
npm run dev:all
```

| URL | Purpose |
|-----|---------|
| http://localhost:8080 | Website |
| http://localhost:3000 | API |
| http://localhost:8080/admin/login | Admin |

### Default accounts (after seed)
| Role | Login | Password |
|------|-------|----------|
| Admin | `admin` | `admin123` |
| IT teacher | `teacher_it` | `teacher123` |
| Physics | `teacher_physics` | `teacher123` |
| Chemistry | `teacher_chemistry` | `teacher123` |

---

## Production build

```bash
npm run build              # Frontend → dist/
cd server && npm run build # Backend → server/dist/
```

Or Docker:
```bash
docker build -t kvant-management .
docker run -p 8080:8080 -e JWT_SECRET=... -v kvant_data:/data kvant-management
```

---

## Security notes (for buyers)

- Change `JWT_SECRET` and `ADMIN_PASSWORD` before any public deploy
- Set `DISABLE_APPLICATIONS=true` on demo sites to prevent spam
- Do not expose default teacher passwords publicly
- SQLite file should live on persistent disk (volume/VPS)

---

## License & purchase

**Price: $50** — one-time payment for full source code.  
**Telegram:** [@history_none](https://t.me/history_none)

Includes:
- Frontend + backend source
- Demo seed data
- Docker + Fly.io configuration
- Setup documentation

Commercial use allowed for buyer. Redistribution/resale of source code is not permitted unless agreed separately.

---

## Support

Questions and purchase: **Telegram [@history_none](https://t.me/history_none)**
