---
name: setup_sse_streaming
description: Implement Server-Sent Events (SSE) streaming for LangGraph agents (FastAPI + React). Includes robust frontend parsing and mandatory fallback logic.
---

# Instructions

## 1. Backend Implementation (FastAPI)
-   **Endpoint**: Create `POST /api/chat/stream`.
-   **Library**: Use `sse-starlette` (`EventSourceResponse`).
-   **Generator**:
    -   Yield dicts: `{"event": "type", "data": json.dumps(obj)}`.
    -   Events: `agent_start`, `thought`, `itinerary`, `reply`, `done`.
    -   **Error Handling**: Yield `event: error` with distinct messages for Auth/Quota issues.
-   **State**: Use `AsyncPostgresSaver` with connection pooling (`get_checkpointer_pool()`) to stream partial updates.

## 2. Frontend Implementation (React)
-   **Library**: Native `fetch` with `ReadableStream` (preferred over `EventSource` for POST support).
-   **Parsing Logic (CRITICAL)**:
    -   SSE chunks often split lines. **Accumulate** the buffer.
    -   Split by `\n\n` (standard) or `\r\n\r\n`.
    -   **Multi-packet Handling**:
        ```typescript
        // ❌ BAD: Overwriting data
        // currentData = line.slice(5).trim();

        // ✅ GOOD: Appending data
        currentData += (currentData ? "\n" : "") + line.slice(5).trim();
        ```

## 3. Resilience & Fallback
-   **Mandatory Fallback**: If SSE fails (network, encoding, timeout), the client **MUST** automatically retry using the standard blocking endpoint (`POST /api/chat`).
-   **Schema Parity**: Ensure `ChatResponse` (blocking) returns the *exact same data structure* (e.g., `itinerary`, `family_profile`) as the stream, so the UI can populate regardless of transport. never leave the UI empty.

## 4. Example: Frontend Reader loop
```typescript
const reader = response.body.getReader();
const decoder = new TextDecoder();
let buffer = "";

while (true) {
  const { done, value } = await reader.read();
  if (done) break;
  buffer += decoder.decode(value, { stream: true });
  // ... split by \n\n and parse ...
}
```
