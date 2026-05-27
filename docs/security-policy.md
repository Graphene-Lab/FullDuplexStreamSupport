# FullDuplexStreamSupport – Security Policy

## Transport Security

`FullDuplexStreamSupport` provides pipe stream abstractions (`PipeStream`, `PipeStreamClient`). The pipes themselves do not implement encryption.

**All callers are responsible for encrypting data before writing to the stream.** In the CloudBox ecosystem, the `EncryptedMessaging` and `CommunicationChannel` layers apply encryption above this abstraction — do not bypass those layers.

## Named Pipe Access Control

Named pipes on Windows and Linux are subject to operating-system access control:

- On **Windows**, named pipe security is controlled via ACLs. Restrict pipe access to the service account and authorised client processes.
- On **Linux**, named pipes (`mkfifo`) inherit directory permissions. Ensure the containing directory has appropriate permissions (e.g., `700` for private use).

## Denial of Service

A slow reader can block a `PipeStream` writer if the pipe buffer fills. Implement write timeouts in callers to prevent indefinite blocking.

## No Authentication at This Layer

`FullDuplexStreamSupport` provides no authentication mechanism. Authentication is the responsibility of higher-level layers (`CommunicationChannel` login protocol, `EncryptedMessaging` key exchange).

## Reporting Vulnerabilities

Open a private GitHub Security Advisory in the repository. Do not disclose vulnerabilities publicly before a fix is available.
