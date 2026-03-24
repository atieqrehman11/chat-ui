# Chat UI — Frontend

A generic, backend-agnostic streaming chat UI built with React 18 and TypeScript.
Ships pre-wired to the [System Design Mentor](https://github.com/atieqrehman11/system-design-reviewer) backend, but the core library is fully decoupled and can be connected to any NDJSON streaming backend.

---

## How It Works

- Streams NDJSON events from a backend and renders them as a live conversation
- Two-phase flow: submit a document → agents stream thinking + results → follow-up chat
- File upload with client-side validation (size + type)
- Pluggable event transformer and report renderer — no domain logic in the core

The frontend is split into two layers:
- `src/core/` — generic, reusable library with no domain knowledge
- `src/integrations/review-backend/` — System Design Mentor-specific wiring

`App.tsx` is the only file that imports from both layers.

---

## Quick Start

```bash
chmod +x dev.sh
./dev.sh          # starts dev server on http://localhost:3000
```

On first run, if no `.env` exists it copies `.env.example` to `.env` and exits — edit the file then re-run.
API requests to `/api/v1/*` are proxied to `http://localhost:8000` automatically in dev.

---

## Project Structure

```
src/
  core/                        # Generic, reusable chat-ui library
    components/                # ChatInterface, MessageList, MessageBubble, InputArea, FileUploader
    config/                    # ChatUIConfig interface + createDefaultChatUIConfig()
    services/                  # HTTP client, NDJSON stream reader, retry, chat client
    types/                     # Message, MessageType, StreamEventTransformer, ...
    utils/                     # id, signal, clipboard
    index.ts                   # Public API barrel export
  integrations/
    review-backend/            # System Design Mentor integration
      transformer.ts           # ReviewResponse → Message
      renderReviewReport.tsx   # ReviewReport renderer
      ReportContent.tsx        # Structured report UI
      constants.ts             # reviewChatUIConfig
      types.ts                 # ReviewReport, ReviewFinding, ReviewScorecard, ReviewResponse
  config/
    constants.ts               # App-level defaults (API URLs, UI text, file config)
  App.tsx                      # Wires core + integration together
```

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
| [Getting Started](docs/getting-started.md) | Install, run, test, build |
| [Architecture](docs/architecture.md) | Component tree, hooks, services, two-phase data flow |
| [Configuration](docs/configuration.md) | All `REACT_APP_*` variables, `ChatUIConfig` reference |
| [Integration Guide](docs/integration-guide.md) | How to wire a different backend |

---

## Commands

```bash
./dev.sh              # start dev server (default)
./dev.sh build        # production build → build/
./dev.sh test         # run all tests once
./dev.sh typecheck    # tsc --noEmit
```
