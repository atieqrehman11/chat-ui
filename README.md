# Chat UI — Frontend

A generic, backend-agnostic streaming chat UI built with React 18 and TypeScript.
Ships pre-wired to the [System Design Mentor](https://github.com/atieqrehman11/system_design_reviewer) backend, but the core library is fully decoupled and can be connected to any NDJSON streaming backend.

---

## Key Features

**Real-time NDJSON streaming**
Reads a `ReadableStream` from any backend and renders events as a live conversation. Buffers partial lines across chunks so no event is ever dropped or split.

**Two-phase conversation flow**
Phase 1 — submit a document and stream agent results. Phase 2 — follow-up chat backed by a direct LLM call. The UI switches modes automatically when the review stream completes, with no page reload or manual wiring required.

**AGENT_THINKING → AGENT_RESULT in-place upsert**
When an agent result arrives, it replaces the corresponding thinking bubble in-place (`mergeAgentResult`). The message list stays clean during multi-agent execution — no stacking of redundant bubbles.

**Pluggable event transformer**
The core has zero knowledge of backend event shapes. Implement `StreamEventTransformer<TEvent>` to map any NDJSON event to a `Message` — two pure methods, no side effects required.

**Pluggable report renderer**
Pass a `renderReport` prop to `ChatInterface` to render structured report data however you like. Falls back to a generic key-value renderer if omitted.

**Exponential backoff retry**
Submission calls retry up to 3 times with exponential backoff (1 s base, ×2 multiplier). 4xx errors and aborted requests are not retried.

**Request timeout + abort signal**
Every HTTP call is wrapped with a configurable timeout (default 30 s for chat, 60 s for submit). Timeout and user-provided `AbortSignal` are composed so either can cancel the request.

**File upload with drag-and-drop**
Supports `.txt`, `.md`, `.json`, `.pdf`, `.doc`, `.docx` up to a configurable size limit (default 5 MB). Client-side validation runs before any network call. Drag-and-drop and click-to-browse both supported.

**Fully env-configurable**
All API endpoints, UI strings, and upload limits are driven by `REACT_APP_*` environment variables — nothing hardcoded. One `createDefaultChatUIConfig()` call builds a complete config from env vars with sensible defaults.

**No global state**
All state lives in `useChatInterface`. No Redux, no Context API, no external store. The hook is exported so you can build a fully custom UI on top of the same logic.

**Correlation ID tracking**
A UUID is generated client-side before each submission and sent as `X-Correlation-ID`. All messages in a session share the same ID, enabling `MessageList` to group them visually across review sessions.

---

## How It Works

```
User submits text / file
        │
        ▼
useChatInterface.handleSubmit()
        │
        ├── Phase 1 (review mode)
        │     useSubmitStream → POST /api/v1/review
        │     parseNDJSONStream → transformer.transform(event)
        │     mergeAgentResult: AGENT_THINKING replaced by AGENT_RESULT in-place
        │     stream ends → isFollowUpMode = true
        │
        └── Phase 2 (follow-up mode)
              useChatStream → POST /api/v1/chat/{correlationId}
              drainChatStream → onChunk updates assistant bubble
              stream ends → chatHistory updated
```

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
