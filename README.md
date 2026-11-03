# WIS — `frontend` Branch

Web interface for the **Wildlife Information System (WIS)**, built with **React 19** and **Vite**. Data entry staff, researchers and admins use it to manage animal records, upload movement data, search and export. It talks to the WIS REST API in [`../backend`](../backend).

> UI wireframes: [`artifacts`](https://github.com/ehtisham-id/wis/tree/artifacts/design) branch · Requirements: [`documentation`](https://github.com/ehtisham-id/wis/tree/documentation) branch

---

## Structure

```
implementation/frontend/
├── index.html            # HTML shell, mounts React into #root
├── vite.config.js        # Vite + @vitejs/plugin-react
├── eslint.config.js      # ESLint flat config (React Hooks, React Refresh)
├── package.json
├── public/               # served as-is (favicon.svg, icons.svg)
└── src/
    ├── main.jsx          # entry point — renders <App /> in StrictMode
    ├── App.jsx           # root component
    ├── App.css
    ├── index.css         # global styles
    └── assets/           # images imported by components
```

---

## Getting Started

**Requirements:** Node.js 22.x and npm.

```bash
git checkout frontend
cd implementation/frontend
npm install
npm run dev               # http://localhost:5173
```

To use real data, start the backend in a second terminal (`cd implementation/backend && npm start`, port 3000).

### Scripts

| Script | What it does |
|---|---|
| `npm run dev` | Dev server with hot module reload |
| `npm run build` | Production build into `dist/` |
| `npm run preview` | Serve the `dist/` build locally |
| `npm run lint` | ESLint on all `.js` / `.jsx` files |

---

## Pages to Build

Current state: `App.jsx` is still the Vite starter page. These are the planned screens, based on the SRS and the interface diagrams:

| Page | Purpose | API used |
|---|---|---|
| Login | Sign in, store JWT | `POST /api/auth/login` |
| Dashboard | Overview and navigation by role | — |
| Animal list & search | Filter by species, location, date; paginated | `GET /api/animals` |
| Animal profile | Details, movements, health, DNA tabs | `GET /api/animals/:id`, `/movements`, `/health`, `/dna` |
| Add / edit animal | Create and update records | `POST` / `PUT /api/animals` |
| Movement upload | Bulk CSV upload with error report | `POST /api/movements/upload` |
| Export | Download search results as CSV / JSON | Export endpoint (TBD) |
| User management | Admin creates users and assigns roles | User endpoint (TBD) |

### User roles

Admin · Data Entry Operator · Researcher · Government Official · Developer. The UI shows or hides actions based on the role in the JWT.

---

## Connecting to the Backend

The dev server runs on port 5173 and the API on port 3000. To avoid CORS errors in development, add a proxy to `vite.config.js`:

```js
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: { '/api': 'http://localhost:3000' },
  },
})
```

Then call the API with relative paths, e.g. `fetch('/api/animals')`, and send the token as `Authorization: Bearer <token>`.

---

## Conventions

- One component per file, `PascalCase.jsx` (e.g. `AnimalCard.jsx`).
- Run `npm run lint` before committing. Fix all errors.
- Don't commit `node_modules/` or `dist/` (already in `.gitignore`).
- Commit prefixes: `feat:`, `fix:`, `style:`, `docs:`.

---

## Deployment

The Azure pipeline (`.github/workflows/implementation_wis.yml`) deploys **only the backend** when `implementation` is pushed. The frontend isn't deployed yet. Options include building it (`npm run build`) and serving `dist/` from the Express `public/` folder, or deploying it separately (e.g. Azure Static Web Apps).

---

## Branch Workflow

```
frontend ──► implementation ──► testing ──► master
```

```bash
git checkout frontend
git pull origin implementation       # stay in sync with backend changes
# ...build UI...
git add implementation/frontend
git commit -m "feat: add animal search page"
git push origin frontend
```

When a feature is done, merge `frontend` into `implementation` (or open a PR). That push triggers the Azure deploy of the backend.
