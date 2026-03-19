# Chat UI — Frontend

Generic streaming chat UI for the System Design Mentor backend.
Built with React 18 and TypeScript.

---

## Quick Start

```bash
npm install
npm start        # dev server on http://localhost:3000
```

Requires the backend running on `http://localhost:8000`.
API requests are proxied automatically in development — no env vars needed locally.

---

## Configuration

All configuration is via `REACT_APP_*` environment variables.

```bash
cp .env.example .env
# edit .env as needed
```

The only variable required in production is `REACT_APP_API_BASE_URL`.
See [docs/configuration.md](docs/configuration.md) for the full reference.

---

## Documentation

| Document | Description |
|---|---|
| [Architecture](docs/architecture.md) | Component tree, hooks, services, data flow |
| [Configuration](docs/configuration.md) | All `REACT_APP_*` variables and their defaults |
| [Getting Started](docs/getting-started.md) | Install, run, test, build |
| [Integration Guide](docs/integration-guide.md) | How to wire a different backend |

---

## Commands

```bash
npm start                         # dev server
npm test -- --watchAll=false      # run all tests once
npm run build                     # production build → build/
npx tsc --noEmit                  # type-check
```
