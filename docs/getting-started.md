# Getting Started

## Prerequisites

- Node.js 18+
- npm
- The backend API running (see backend repo)

---

## Install and Run

```bash
cd frontend
npm install
npm start
```

Frontend starts on http://localhost:3000.
API requests to `/api/v1/*` are proxied to `http://localhost:8000` via `setupProxy.js`.

---

## Environment Variables

Create `frontend/.env`:

```bash
REACT_APP_API_BASE_URL=http://localhost:8000/api/v1
```

If not set, defaults to `/api/v1` (relative URL — works with the dev proxy).

---

## Dev Commands

```bash
npm start                          # dev server on :3000
npm test -- --watchAll=false       # run all tests once
npm run build                      # production build → frontend/build/
npx tsc --noEmit                   # type-check without emitting
```

---

## Running Tests

```bash
npm test -- --watchAll=false
```

Tests live in `__tests__/` folders co-located with the code they test.
All 18 test suites must pass before merging.

---

## Production Build

```bash
npm run build
```

Outputs to `frontend/build/`. Serve as static files behind any web server or CDN.
Set `REACT_APP_API_BASE_URL` to the production backend URL before building.
