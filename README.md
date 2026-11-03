# WIS — `testing` Branch

This branch is where the **Wildlife Information System (WIS)** is tested. It pulls in the latest code from `implementation`, runs it, executes the test cases, and stores the evidence and results.

> Full test plan and acceptance criteria: [SRS Chapter 6 — Testing](documentation/srs/6-testing/testing.pdf)

---

## Branch Contents

```
wis/  (testing)
├── documentation/srs/6-testing/
│   ├── testing.docx            # Chapter 6 (editable)
│   └── testing.pdf             # Chapter 6 (final)
├── implementation/             # Code under test (synced from `implementation`)
│   ├── backend/                # Node.js + Express API
│   └── frontend/               # React + Vite UI
└── testing/
    └── network-trace/          # DevTools HAR captures per test case
```

### Related Branches

| Branch | Role in testing |
|---|---|
| `implementation` | Source of the code under test, pulled in here |
| `backend` / `frontend` | Where bugs found here get fixed |
| `documentation` | Gets the updated Chapter 6 with results |
| `artifacts` | Analysis and design diagrams (reference only) |
| `master` | Gets the tested work merged in |

---

## Setup

**Requirements:** Node.js (LTS), npm, Postman, a Chromium/Firefox browser.

```bash
git checkout testing
git pull origin implementation      # get the latest code to test
```

**Backend** — http://localhost:3000

```bash
cd implementation/backend
npm install
npm start
```

**Frontend** — http://localhost:5173

```bash
cd implementation/frontend
npm install
npm run dev
npm run lint                        # static checks
```

---

## Running the Tests

1. Start the backend (and the frontend for UI tests).
2. In Postman, set an environment variable `baseUrl = http://localhost:3000`.
3. Run **TC-01** first and save the returned JWT as `token`; send it as `Authorization: Bearer {{token}}` in the protected requests.
4. Run each test case below, compare the result with the expected output, and update the **Status** column.
5. For UI tests, open DevTools → Network, do the test, then *Save all as HAR* into `testing/network-trace/`.

---

## Test Case Tracker

Status: ⬜ Not run · ✅ Pass · ❌ Fail · ⏸️ Blocked (endpoint not implemented)

| TC | Feature | Request | Input | Expected | Status |
|---|---|---|---|---|---|
| TC-01 | Login | `POST /api/auth/login` | Valid email + password | `200`, JWT returned | ⏸️ |
| TC-02 | Login (wrong) | `POST /api/auth/login` | Wrong password | `401` | ⏸️ |
| TC-03 | Add animal | `POST /api/animals` | Valid animal fields | Record saved, new ID | ⏸️ |
| TC-04 | Add animal (missing) | `POST /api/animals` | No species name | `400` + field error | ⏸️ |
| TC-05 | Get animal | `GET /api/animals/:id` | Existing ID | Animal JSON | ⏸️ |
| TC-06 | Get animal (wrong ID) | `GET /api/animals/:id` | Unknown ID | `404` | ⏸️ |
| TC-07 | Search by species | `GET /api/animals?species=Snow Leopard` | Query param | Matching animals | ⏸️ |
| TC-08 | CSV upload | `POST /api/movements/upload` | Valid CSV | All rows saved | ⏸️ |
| TC-09 | CSV upload (bad) | `POST /api/movements/upload` | CSV missing coordinates | Bad rows skipped, error report | ⏸️ |
| TC-10 | No token | `GET /api/animals` | No `Authorization` header | `401` | ⏸️ |
| TC-11 | Export data | Export endpoint (TBD) | Search results | CSV/JSON download | ⏸️ |
| TC-12 | Admin creates user | User endpoint (TBD) | User details + role | Account created | ⏸️ |

> All cases are ⏸️ Blocked for now: the backend is still the Express starter (`/` and `/users` routes only). Change each status as its endpoint lands in `implementation`.

---

## Evidence & Bug Reporting

- **File naming:** `TC-XX_<short-name>.<ext>`, e.g. `testing/network-trace/TC-01_login_success.har`
- **Postman:** export the collection to `testing/WIS.postman_collection.json` after each test round.
- **Failures:** open a GitHub issue titled `[TC-XX] <what failed>` with the request, expected vs. actual result, and the HAR or screenshot. Fix it in `backend` or `frontend`, then test again here.

---

## Branch Workflow

```bash
git checkout testing
git pull origin implementation               # sync the code under test
# ...run tests, update tracker, add evidence...
git add README.md testing/
git commit -m "test: run TC-01..TC-06, login and animal endpoints"
git push origin testing
```

When every test case passes, open a pull request from **`testing` → `master`** and copy the final results into Chapter 6 on `documentation`.
