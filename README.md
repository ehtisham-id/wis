# WIS — `backend` Branch

REST API for the **Wildlife Information System (WIS)**, built with **Node.js** and **Express**. It stores and serves animal profiles, movement tracking, DNA and health records, with JWT authentication and role-based access. The [`../frontend`](../frontend) web UI and external apps use it.

> API spec and data model: [`documentation`](https://github.com/ehtisham-id/wis/tree/documentation) branch (SRS Ch. 3–5) · ER / class / sequence diagrams: [`artifacts`](https://github.com/ehtisham-id/wis/tree/artifacts) branch

---

## Structure

```
implementation/backend/
├── bin/www               # entry point — creates HTTP server on PORT (default 3000)
├── app.js                # Express app: middleware + route mounting
├── routes/
│   ├── index.js          # /
│   └── users.js          # /users
├── public/               # static files served at /
│   ├── index.html
│   └── stylesheets/style.css
├── package.json
└── package-lock.json
```

### Planned layout

```
├── config/               # DB connection, env loading
├── middleware/           # auth (JWT verify), role check, error handler
├── models/               # Animal, Movement, Health, DNA, User queries
├── controllers/          # request handling per resource
└── routes/               # auth, animals, movements, users
```

---

## Tech Stack

| Purpose | Package / Tool | Status |
|---|---|---|
| Runtime | Node.js 22.x | ✅ |
| Web framework | Express 4 | ✅ |
| Request logging | morgan | ✅ |
| Cookies | cookie-parser | ✅ |
| Database | PostgreSQL (`pg`) | Planned |
| Auth | JSON Web Tokens (`jsonwebtoken`), `bcrypt` | Planned |
| CSV upload | `multer` + CSV parser | Planned |
| Config | `dotenv` | Planned |

---

## Getting Started

**Requirements:** Node.js 22.x, npm. PostgreSQL once the database layer is added.

```bash
git checkout backend
cd implementation/backend
npm install
npm start                 # http://localhost:3000
```

Change the port with `PORT` (PowerShell: `$env:PORT=4000; npm start` · bash: `PORT=4000 npm start`).

Debug logging: `$env:DEBUG="backend:*"; npm start`

### Environment variables (planned)

Create `implementation/backend/.env`. Never commit it.

| Variable | Example | Used for |
|---|---|---|
| `PORT` | `3000` | Server port |
| `DATABASE_URL` | `postgres://user:pass@localhost:5432/wis` | PostgreSQL connection |
| `JWT_SECRET` | long random string | Signing tokens |
| `JWT_EXPIRES_IN` | `1h` | Token lifetime |

---

## API

### Available now

| Method | Endpoint | Response |
|---|---|---|
| `GET` | `/` | Static `public/index.html` |
| `GET` | `/users` | Placeholder text |

### Planned

All `/api` routes except login need `Authorization: Bearer <token>`.

| Method | Endpoint | Description | Roles |
|---|---|---|---|
| `POST` | `/api/auth/login` | Log in, returns JWT | Public |
| `GET` | `/api/animals` | List / search (species, location, date range, pagination) | All |
| `GET` | `/api/animals/:id` | One animal | All |
| `POST` | `/api/animals` | Create animal | Admin, Data Entry |
| `PUT` | `/api/animals/:id` | Update animal | Admin, Data Entry |
| `DELETE` | `/api/animals/:id` | Delete animal | Admin |
| `GET` | `/api/animals/:id/movements` | Movement history | All |
| `POST` | `/api/movements/upload` | Bulk CSV movement upload | Admin, Data Entry |
| `GET` | `/api/animals/:id/health` | Health / vet records | All |
| `GET` | `/api/animals/:id/dna` | DNA records | All |
| `POST` | `/api/users` | Create user with role | Admin |

### Response conventions

| Status | When |
|---|---|
| `200` / `201` | Success / created |
| `400` | Validation error, e.g. missing species. Body: `{ "error": "...", "field": "..." }` |
| `401` | Missing or invalid token |
| `403` | Valid token, role not allowed |
| `404` | Record not found |
| `500` | Server error. Don't leak stack traces |

These match test cases TC-01 to TC-12 on the `testing` branch.

---

## Deployment

The backend is the part that gets deployed. [`.github/workflows/implementation_wis.yml`](../../.github/workflows/implementation_wis.yml) runs on every push to **`implementation`** (not `backend`):

1. `npm ci` in `implementation/backend`, then `npm test` (if a `test` script exists)
2. Deploy this folder to the Azure Web App **`wis`** (Production slot)

Before merging into `implementation`:
- Commit `package-lock.json` along with `package.json`. Otherwise `npm ci` fails.
- Read the port from `process.env.PORT` (already done in `bin/www`). Azure sets it.
- Put secrets (`DATABASE_URL`, `JWT_SECRET`) in Azure App Service → **Configuration**, not in code.

---

## Known Issues

- `routes/index.js` calls `res.render('index')`, but no view engine is configured. It works only because `public/index.html` is served first by `express.static`. Replace it with an API health route (e.g. `GET /api/health`) or remove it.
- There's no `test` script yet, so the CI test step is skipped.

---

## Branch Workflow

```
backend ──► implementation ──► testing ──► master
                 │
                 └──► Azure (auto-deploy)
```

```bash
git checkout backend
git pull origin implementation       # stay in sync with frontend changes
# ...build API...
git add implementation/backend
git commit -m "feat: add animals CRUD endpoints"
git push origin backend
```

When an endpoint is ready, merge `backend` into `implementation` (or open a PR), then test it on `testing`.
