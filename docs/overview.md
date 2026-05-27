# FullDuplexStreamSupport – System Overview

## Purpose

**FullDuplexStreamSupport** (package: `FullDuplexStreamSupport`) provides a `PipeStream` abstraction that multiplexes multiple independent full-duplex logical streams over a single pair of `Stream` objects (input + output).

This solves a common problem in inter-process communication and cloud architectures: when multiple independent consumers need to share a single byte-oriented channel without collisions.

## Key Features

- **Full-duplex**: simultaneous read and write over the same channel.
- **Multi-client**: a single `PipeStream` instance manages connections from multiple logical clients via client IDs.
- **Sync & async**: both synchronous and `async/await` read/write APIs.
- **Timeout support**: configurable connection timeout.
- **Thread-safe**: concurrent access is internally synchronized.
- **No-collision single-channel**: prevents input/output interleaving on shared streams.

## Architecture

```
Application (multiple logical clients)
	│
	├── PipeStreamClient [clientId=1]
	├── PipeStreamClient [clientId=2]
	└── PipeStreamClient [clientId=n]
			│
			▼
		PipeStream  (multiplexer / demultiplexer)
			│
	   ┌────┴────┐
	Stream_In   Stream_Out  (e.g., named pipe, TCP, memory stream)
```

`PipeStreamClientToStreamAdapter` wraps a `PipeStreamClient` in the standard `Stream` interface for interoperability with libraries that require `Stream`.

## Typical Use Cases

- **IPC between .NET processes** over named pipes.
- **Cloud-to-cloud communication** over a shared TCP connection.
- **Blazor SignalR** real-time data multiplexing.
- **RPC** layers built on top of a single connection.

## Target Framework

**.NET Standard 2.1**
