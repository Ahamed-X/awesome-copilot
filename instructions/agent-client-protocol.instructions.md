---
description: 'Coding standards and best practices for implementing and integrating with the Agent Client Protocol (ACP)'
applyTo: '**'
---

# Agent Client Protocol (ACP) Development

The Agent Client Protocol (ACP) standardizes communication between code editors/IDEs and coding agents. It is suitable for both local and remote scenarios.

## Overview

ACP assumes that the user is primarily in their editor and wants to reach out and use agents to assist them with specific tasks.

- **Local agents** run as sub-processes, communicating via JSON-RPC over stdio.
- **Remote agents** communicate over HTTP or WebSocket.

## Communication Model

The protocol follows the [JSON-RPC 2.0](https://www.jsonrpc.org/specification) specification:

- **Methods**: Request-response pairs that expect a result or error.
- **Notifications**: One-way messages that do not expect a response.

## Message Flow

A typical flow follows this pattern:

1. **Initialization Phase**
   - Client → Agent: `initialize` to establish connection.
   - Client → Agent: `authenticate` if required by the Agent.

2. **Session Setup**
   - Client → Agent: `session/new` to create a new session.
   - Client → Agent: `session/load` to resume an existing session (if supported).

3. **Prompt Turn**
   - Client → Agent: `session/prompt` to send user message.
   - Agent → Client: `session/update` notifications for progress updates (message chunks, tool calls, plans).
   - Turn ends when the Agent sends the `session/prompt` response with a stop reason.

## Baseline Methods

### Agent Methods

- `initialize`: Negotiate versions and exchange capabilities.
- `authenticate`: Authenticate with the Agent.
- `session/new`: Create a new conversation session.
- `session/prompt`: Send user prompts to the Agent.

### Client Methods

- `session/request_permission`: Request user authorization for tool calls.

## Optional Methods & Features

- `session/load`: Load an existing session.
- `logout`: End the current authenticated state.
- `session/set_mode`: Switch between agent operating modes (e.g., 'architect', 'code').
- `fs/*`: File system access methods (read, write).
- `terminal/*`: Terminal execution and management.

## Best Practices

- **Absolute Paths**: All file paths in the protocol MUST be absolute.
- **1-based Line Numbers**: Line numbers are 1-based.
- **CamelCase Keys**: Use `camelCase` for JSON object property keys.
- **Snake_case Discriminators**: Use `snake_case` for string values carried by discriminator fields.
- **Markdown Formatting**: Use Markdown for user-readable text to ensure rich formatting across editors.
- **Error Handling**: Follow standard JSON-RPC 2.0 error handling. Successful responses include a `result` field; errors include an `error` object with `code` and `message`.
- **Cancellation**: Implement `session/cancel` to allow users to interrupt long-running operations.

## Extensibility

- **_meta fields**: Add custom data to standard messages using `_meta` fields.
- **Custom Methods**: Prefix custom method names with an underscore (`_`).
- **Capabilities**: Advertise custom capabilities during the `initialize` phase.

## Resources

- [Official Documentation](https://agentclientprotocol.com)
- [Protocol Specification](https://agentclientprotocol.com/protocol/v1/overview)
- [JSON-RPC 2.0 Specification](https://www.jsonrpc.org/specification)
