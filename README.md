# WIS — `implementation` Branch

Source code for the **Wildlife Information System (WIS)**: a Node.js + Express REST API and a React + Vite web interface. Every push to this branch is built and deployed to **Azure App Service** by GitHub Actions.

> Requirements and design this code follows: [`documentation`](https://github.com/ehtisham-id/wis/tree/documentation) branch (SRS) and [`artifacts`](https://github.com/ehtisham-id/wis/tree/artifacts) branch (diagrams).

---

## Structure

```
wis/  (implementation)
├── .github/workflows/
│   └── implementation_wis.yml   # CI/CD: build backend → deploy to Azure
└── implementation/
    ├── README.md                # this file
    ├── backend/                 # REST API — Node.js + Express
    │   ├── bin/www              # server entry point (PORT, default 3000)
    │   ├── app.js               # Express app, middleware, route mounting
    │   ├── routes/
    │   │   ├── index.js         # /
    │   │   └── users.js         # /users
    │   ├── public/              # static files (index.html, stylesheets/)
    │   └── package.json
    └── frontend/                # Web UI — React 19 + Vite
        ├── index.html
        ├── src/
        │   ├── main.jsx         # React entry point
        │   ├── App.jsx          # root component
        │   └── assets/
        ├── public/
        ├── eslint.config.js
        ├── vite.config.js
        └── package.json
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, Vite, ESLint |
| Backend | Node.js 22, Express 4, morgan, cookie-parser |
| Database (planned) | PostgreSQL |
| Auth (planned) | JWT |
| CI/CD | GitHub Actions → Azure App Service (`wis`) |

---

## Getting Started

**Requirements:** Node.js 22.x, npm, Git.

```bash
git clone https://github.com/ehtisham-id/wis.git
cd wis
git checkout implementation
```

### Backend

```bash
cd implementation/backend
npm install
npm start                 # http://localhost:3000
```

Set a different port with the `PORT` environment variable (PowerShell: `$env:PORT=4000; npm start`).

### Frontend

```bash
cd implementation/frontend
npm install
npm run dev               # http://localhost:5173
```

| Script | What it does |
|---|---|
| `npm run dev` | Start the Vite dev server with hot reload |
| `npm run build` | Production build into `dist/` |
| `npm run preview` | Serve the production build locally |
| `npm run lint` | Run ESLint |

---

## API

### Available now

| Method | Endpoint | Response |
|---|---|---|
| `GET` | `/` | Static `public/index.html` |
| `GET` | `/users` | Placeholder text |

### Planned (from the SRS)

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/login` | Log in, returns a JWT |
| `GET` | `/api/animals` | List animals (filters: species, location, date) |
| `GET` | `/api/animals/:id` | Get one animal |
| `POST` | `/api/animals` | Add an animal |
| `PUT` | `/api/animals/:id` | Update an animal |
| `DELETE` | `/api/animals/:id` | Delete an animal |
| `GET` | `/api/animals/:id/movements` | Movement history |
| `POST` | `/api/movements/upload` | Bulk CSV movement upload |
| `GET` | `/api/animals/:id/health` | Health records |
| `GET` | `/api/animals/:id/dna` | DNA records |

---

## Deployment

[`.github/workflows/implementation_wis.yml`](../.github/workflows/implementation_wis.yml) runs on every push to `implementation`, and can also be started by hand from the **Actions** tab:

1. Check out the code and set up Node.js 22.
2. In `implementation/backend`, run `npm ci` and `npm test` (if a test script exists).
3. Log in to Azure using the repository's OIDC secrets.
4. Deploy `implementation/backend` to the Azure Web App **`wis`** (Production slot).

Only the **backend** is deployed. The frontend is not part of the pipeline yet.

> `npm ci` needs `package-lock.json` to match `package.json`. After adding a dependency, commit both files or the deploy fails.

---

## Branch Workflow

Feature work happens in `backend` and `frontend`, gets merged here, deployed, then tested on `testing`.

```
backend  ─┐
          ├──► implementation ──► testing ──► master
frontend ─┘        │
                   └──► Azure (auto-deploy)
```

```bash
git checkout implementation
git pull origin backend          # bring in API work
git pull origin frontend         # bring in UI work
git push origin implementation   # triggers the Azure deploy
```

Commit message prefixes used in this repo: `feat:`, `fix:`, `docs:`, `test:`.
