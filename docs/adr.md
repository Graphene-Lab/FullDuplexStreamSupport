# FullDuplexStreamSupport – Architecture Decision Records

## ADR-001: Stream-Pair Multiplexing Over Named Pipes

**Date**: 2024  
**Status**: Accepted

### Context
Named pipes in .NET expose a single `Stream`. When multiple logical channels need to share the same pipe, they collide — a write from channel A may interleave with a write from channel B.

### Decision
Implement a **multiplexer/demultiplexer** on top of any `Stream` pair. Each logical channel (identified by a `clientId`) frames its data with a 4-byte length prefix and channel ID. The `PipeStream` dispatches incoming frames to the correct `PipeStreamClient`.

### Consequences
- **Positive**: N logical channels share one physical pipe without collision.
- **Positive**: works over any `Stream`, not just named pipes (TCP, memory, etc.).
- **Negative**: adds a small framing overhead (8 bytes per message: 4 bytes length + 4 bytes channel ID).

---

## ADR-002: Standard Stream Adapter

**Date**: 2024  
**Status**: Accepted

### Context
Many .NET libraries (e.g., compression, serialization, crypto) require a standard `Stream` object. `PipeStreamClient` has its own API and is not a `Stream`.

### Decision
Provide `PipeStreamClientToStreamAdapter`, which wraps a `PipeStreamClient` and exposes it as a `Stream`.

### Consequences
- **Positive**: zero-friction integration with any `Stream`-based .NET API.
- **Positive**: no changes needed to existing code that expects a `Stream`.
- **Negative**: the adapter forwards calls synchronously; async pipelines must use the native async methods of `PipeStreamClient` directly for best performance.

---

## ADR-003: Configurable Connection Timeout

**Date**: 2024  
**Status**: Accepted

### Context
In distributed cloud scenarios, a client may need to wait for the server to become available. Blocking indefinitely is unacceptable in production.

### Decision
`PipeStreamClient.Connect` accepts a `timeoutMs` parameter. If the connection is not established within the timeout, a `TimeoutException` is thrown.

### Consequences
- **Positive**: callers can implement retry logic with predictable latency budgets.
- **Positive**: `-1` allows indefinite wait when appropriate.
- **Negative**: callers must handle `TimeoutException`; no default automatic retry (by design — retry policy belongs to the caller).
