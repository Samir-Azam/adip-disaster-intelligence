# ADR-009: WebSockets for real-time incident/resource updates

## Status
Accepted

## Context
Operations dashboards (district admin, field officer) must reflect new incidents, AI
completion, status changes, and resource assignment without manual refresh — a named
MVP acceptance criterion. Update volume is modest (single-org/demo scale), and updates
are naturally event-driven (something changed in the DB, tell subscribed clients).

## Decision
Use WebSockets (via `socket.io` or the `ws` library with a thin room/topic layer) from
the Node backend. Authenticate the WebSocket connection using the same JWT used for
REST calls, and scope broadcast "rooms" by role/organization/incident so a client only
receives events it's authorized to see (e.g., a citizen only gets updates for their own
incidents; a district admin gets org-wide incident events).

## Alternatives considered
- **Short-interval polling** — much simpler and easier to reason about, and kept as an
  explicit *fallback* if a client's WebSocket connection drops, but polling alone is a
  worse user experience for an "operations dashboard" use case and doesn't meet the
  "without manual refresh" acceptance criterion as naturally. Rejected as primary
  mechanism, retained as documented fallback.
- **Server-Sent Events (SSE)** — simpler unidirectional alternative, viable for
  read-only dashboards, but WebSockets are chosen for consistency (one real-time
  mechanism, not two) and to leave room for future bidirectional needs (e.g. live
  officer location). Rejected in favor of a single WebSocket mechanism.
- **Third-party real-time backend (e.g. a hosted pub/sub service)** — adds an external
  dependency and cost inappropriate for a self-hosted local MVP. Rejected.

## Consequences
- WebSocket authorization must be re-checked, not assumed from a successfully-opened
  socket — room membership must be derived from the authenticated user's role/org on
  connect, mirroring REST RBAC rather than duplicating separate logic.
- Events (`incident:created`, `incident:status_changed`, `ai:completed`,
  `resource:assigned`, `notification:new`) must be a small, documented, versioned set —
  not ad hoc — so the frontend and handbook stay in sync.
- Reconnect behavior (client attempts to reconnect and re-subscribe to its rooms) must
  be implemented and documented as a known limitation area if not fully hardened in
  MVP (see `15_IMPLEMENTATION_HANDBOOK.md` §9).

## Failure mode notes
If the WebSocket connection is unavailable, the frontend must still function via
regular REST fetches — real-time is a UX enhancement layered on top of a fully
functional request/response API, never a hard dependency for core functionality.
