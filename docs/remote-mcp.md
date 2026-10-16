# Remote MCP and client configuration

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

## Endpoint and transport

Endpoint: `https://ai.freechemdraw.com/mcp`.
Protocol: Model Context Protocol over Streamable HTTP. A client negotiates its MCP
protocol version during initialization. This is not a legacy SSE-only transport.
The hosting address, REST route version, MCP protocol version and documentation
package date are different identifiers.

## Generic connection descriptor

See [mcp-client-config.json](../examples/mcp-client-config.json).

```json
{"transport":"streamable-http","url":"https://ai.freechemdraw.com/mcp"}
```

This is a vendor-neutral descriptor, not a universally importable client schema.
In a client supporting remote Streamable HTTP, enter the URL in its remote-server
settings and select that transport. Consult the client's documentation for its
configuration field names. No official certification of Claude, Cursor or other
client products is claimed.

Official C# and TypeScript SDK interoperability has been covered by the project's
existing tests. A public HTTPS protocol check also covered initialization,
discovery and a synthetic chemical call; this is not certification of all SDK
versions or client applications. Response handling must accept the response modes
negotiated by the transport, including JSON and SSE where applicable.

## Connection sequence

Initialize through the client's MCP transport, complete its initialization flow,
request `tools/list`, then invoke a listed name through `tools/call`. Do not use a
platform login cookie as an MCP OAuth credential. Basic chemical access is anonymous
and rate-limited under the current contract; account-based MCP authentication and
API Key issuance are not offered by that contract.

Successful tool results expose matching JSON text and `structuredContent`.
Domain failures set `isError=true`; protocol/schema failures are JSON-RPC errors.
See [tools](tools.md), [limits](usage-limits.md), and [troubleshooting](troubleshooting.md).

## Deployment-specific capabilities

Four core tools are described in [tools](tools.md). Optional `get_editor_link`
appears only when enabled; it requires explicit consent to temporary storage.
Use [capability discovery](https://ai.freechemdraw.com/api/v1/capabilities) and the
actual tool list rather than assuming every deployment exposes identical features.
