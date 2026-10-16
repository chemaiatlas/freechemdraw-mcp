# Frequently asked questions

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

## What does this repository provide?

Official documentation, connection guidance, request examples and community files
for the FreeChemDraw hosted MCP service. The privately maintained server and
chemical engine are not distributed here. Public documentation does not change
the server implementation's licensing or ownership.

## Do I need an account or API Key?

Basic chemical MCP/REST calls use anonymous, free, rate-limited access under the
current public contract. A web account does not issue keys, paid credits or higher
chemical quotas. Check the current deployment's [capabilities](https://ai.freechemdraw.com/api/v1/capabilities).

## Which clients work?

Use a client supporting Remote MCP with Streamable HTTP. The connection descriptor
is generic; no Claude/Cursor or other client-product certification is claimed.
See [client configuration](remote-mcp.md).

## What formats and tools are supported?

Four core tools normalize SMILES, convert SMILES/MOL, calculate six properties and
render SVG/PNG. Resolve queries accept SMILES only. Names/CAS are unavailable;
ACS-specific drawing style is unsupported. See [tool reference](tools.md).

## Can I edit a structure online?

Manually import SMILES or MOL into [FreeChemDraw](https://freechemdraw.com/).
If the deployment advertises `get_editor_link`, it supports a consent-based,
encrypted temporary, one-time import link. It is optional; read [privacy](privacy.md).

## Are the values experimentally measured?

No. Properties are calculated and require scientific judgment. This service does
not certify every molecule or replace experimental validation.

## Why can I receive 429 even without many tool calls?

Initialization and tool discovery consume HTTP requests, and processing capacity
is shared. Follow `Retry-After` and [usage guidance](usage-limits.md).

## Does the documentation date identify the service version?

No. Documentation bundles use their own date labels. The platform API version,
REST route version and negotiated MCP protocol version are separate identifiers.
Re-exporting documentation does not release a new server version.

## How can I contribute?

Submit documentation corrections, safe examples, bug reports or feature suggestions.
The server source is private and is not part of external pull requests. Read
[contributing](../CONTRIBUTING.md) and [security reporting](../SECURITY.md).
