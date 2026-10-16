# Getting started

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

## Choose Remote MCP or REST

MCP lets an AI client discover named tools and call them with structured arguments.
FreeChemDraw uses Streamable HTTP at `https://ai.freechemdraw.com/mcp`.
REST is a separate JSON API for ordinary application integrations.
Basic chemical calls use anonymous, free, rate-limited access without registration
or an API Key under the current contract. Check deployed capabilities and limits;
a platform web login is not MCP OAuth.

## Connect an MCP client

1. In a client supporting remote Streamable HTTP, add the hosted URL above.
2. Connect and run tool discovery (`tools/list`).
3. Pass this ethanol example to `tools/call` through the client:

```json
{"name":"calculate_properties","arguments":{"structure":"CCO","input_format":"smiles"}}
```

The client supplies the JSON-RPC envelope, initialization, headers and protocol
negotiation. Do not POST the bare object above directly to the MCP endpoint.
On success, `structuredContent` contains the calculated result; text content
contains the matching JSON. See [tool result fields](tools.md).

The [generic connection descriptor](../examples/mcp-client-config.json) describes
transport and URL only. Translate its fields into your client's settings; it is
not a verified vendor-specific configuration. See [client guidance](remote-mcp.md).

## Try REST

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles"}'
```

From this documentation repository's root, the equivalent file-based request is:

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data @examples/rest-calculate-properties.json
```

This uses the [REST body example](../examples/rest-calculate-properties.json).
Successful responses have `request_id`, `data`, `error=null` and `usage.calls=1`.
No server response is reproduced here as if captured from the current deployment.

## Discover the deployment

- [Capabilities](https://ai.freechemdraw.com/api/v1/capabilities): enabled tools,
  supported formats and authentication mode.
- [OpenAPI](https://ai.freechemdraw.com/openapi/v1.json): enabled REST routes and schemas.
- [Official documentation](https://ai.freechemdraw.com/docs/): current operational limits.

Four core tools are described in [tools](tools.md). Use optional `get_editor_link`
only if it is advertised and the user explicitly consents. Read [privacy](privacy.md)
before sending structures. Continue with [REST](rest-api.md), [limits](usage-limits.md)
or [troubleshooting](troubleshooting.md).
