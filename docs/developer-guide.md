# FullDuplexStreamSupport – Developer Guide

## Installation

```bash
dotnet add package FullDuplexStreamSupport
```

## Initialization

```csharp
using FullDuplexStreamSupport;

Stream pipeIn  = /* your input stream  */;
Stream pipeOut = /* your output stream */;

PipeStream.Initialize(pipeIn, pipeOut, OnNewClientConnected);

void OnNewClientConnected(PipeStreamClient client)
{
	// A new logical client has connected
	_ = HandleClientAsync(client);
}
```

## Connecting a Client

```csharp
var client = new PipeStreamClient(clientId: 42);
client.Connect(timeoutMs: 5000);
```

## Writing Data

```csharp
byte[] data = Encoding.UTF8.GetBytes("Hello");
await client.WriteAsync(data, 0, data.Length);
```

## Reading Data

```csharp
byte[] buffer = new byte[4096];
int read = await client.ReadAsync(buffer, 0, buffer.Length);
```

## Using as a Standard Stream

When a library requires a `Stream` object:

```csharp
var adapter = new PipeStreamClientToStreamAdapter(client);
// Pass adapter wherever a Stream is expected
```

## Timeout Handling

If `Connect` does not complete within `timeoutMs`, a `TimeoutException` is thrown. Set `timeoutMs: -1` for no timeout (wait indefinitely).

## Thread Safety

All public methods on `PipeStream` and `PipeStreamClient` are thread-safe. Multiple threads may read and write concurrently.

## Disposal

```csharp
client.Dispose();   // closes the logical client channel
```

Disposing the `PipeStream` itself closes all client connections and the underlying streams.

## Building

```powershell
dotnet build ..\..\FullDuplexStreamSupport\FullDuplexStreamSupport.csproj
```
