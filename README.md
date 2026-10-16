# FreeChemDraw MCP Server

[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md)

**Chemical Structure Tools for AI Agents**

Official documentation and integration examples for FreeChemDraw MCP Server:
remote chemical structure conversion, molecular properties and 2D rendering.

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

[FreeChemDraw](https://freechemdraw.com/) |
[Product overview](https://freechemdraw.com/en/mcp/) |
[Hosted platform](https://ai.freechemdraw.com/) |
[Official documentation](https://ai.freechemdraw.com/docs/)

Remote MCP endpoint: `https://ai.freechemdraw.com/mcp`

## Quick Start

1. Choose a client with remote Streamable HTTP support.
2. Set its server URL to:

   ```text
   https://ai.freechemdraw.com/mcp
   ```

3. Connect, list tools and invoke the following ethanol example through the client:

   ```json
   {"name":"calculate_properties","arguments":{"structure":"CCO","input_format":"smiles"}}
   ```

This object is the payload for `tools/call`, not a complete HTTP/JSON-RPC request.
The client handles initialization and protocol negotiation. Results are calculated,
not experimental measurements. See [getting started](docs/getting-started.md).

## What is FreeChemDraw MCP?

FreeChemDraw MCP Server is a hosted service for AI agents and applications.
Provider: FreeChemDraw. Category: Remote MCP Server / Chemical REST API.
It uses the Model Context Protocol (MCP) with Streamable HTTP to let compatible
clients discover and call chemical tools. This repository contains usage guides
and request examples, not the server implementation or a chemical engine.

## Features

- Free basic chemical calls with anonymous, rate-limited access; no registration
  or API Key is required under the current public contract.
- Normalize SMILES and convert SMILES/MOL structures.
- Calculate molecular formula, molecular weight, exact mass, formal charge, LogP and TPSA.
- Generate SVG/PNG 2D molecular diagrams.
- Integrate chemistry into AI agent workflows through Remote MCP or REST.

Access policy and effective limits follow the hosted deployment. Check
[capabilities](https://ai.freechemdraw.com/api/v1/capabilities) and the current
[service documentation](https://ai.freechemdraw.com/docs/).

## Available Tools

| Core tool | Function |
| --- | --- |
| `resolve_chemical` | SMILES normalization, molecular formula and optional properties |
| `convert_structure` | SMILES/MOL structure conversion |
| `calculate_properties` | Molecular formula, molecular weight, exact mass, formal charge, LogP and TPSA |
| `render_structure` | SVG/PNG 2D structure rendering using the default style |

`get_editor_link` is optional and appears only when enabled by the deployment.
It requires explicit consent (`allow_temporary_storage=true`) for encrypted,
one-time temporary storage; links expire after 10 minutes. Check `tools/list`
before using it. Manual SMILES/MOL import into the online editor is an alternative.
See [tools](docs/tools.md) and [privacy](docs/privacy.md).

## Continue Editing in FreeChemDraw

1. Ask your agent for normalized SMILES, or use `convert_structure` with
   `output_format=mol` to obtain a MOL structure.
2. Open the [FreeChemDraw online editor](https://freechemdraw.com/), paste the
   SMILES or import the saved MOL file.
3. Inspect and continue editing the structure in the editor. Converting MOL to
   SMILES loses coordinates and annotations; use MOL when you need to retain them.

Automatic editor links are available only if the actual deployment advertises
`get_editor_link` in `tools/list`. The tool requires explicit user consent to
temporary encrypted storage; links are single-use and expire after 10 minutes.
If the tool is absent or consent is not given, use manual import. This repository
does not promise automatic synchronization between your client and the editor.
See [tools](docs/tools.md) and [privacy](docs/privacy.md).

## MCP Client Configuration

The [configuration example](examples/mcp-client-config.json) is a vendor-neutral
connection descriptor: `transport=streamable-http`, URL as above. Enter these values
in your client's current remote-server settings; JSON field names differ by client.
It is not a certified, drop-in configuration for Claude, Cursor or another product.
Do not use a stdio command or a legacy SSE-only transport. See [Remote MCP](docs/remote-mcp.md).

## REST API

Send JSON to the hosted chemical REST API. No MCP framing is used for REST:

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles"}'
```

A successful response has `request_id`, `data`, `error=null` and `usage.calls=1`.
`usage.calls` is a call count, not paid credits. See [REST API](docs/rest-api.md)
and the hosted [OpenAPI contract](https://ai.freechemdraw.com/openapi/v1.json).

## Usage Limits

Free basic access is rate-limited and has no service-level guarantee. Initialization
and discovery count toward usage; on HTTP 429, follow `Retry-After` and retry with
backoff. See [usage limits and errors](docs/usage-limits.md) for detailed reference
limits, and the [official service documentation](https://ai.freechemdraw.com/docs/)
for effective production settings.

## Privacy

Requests send structures to the hosted service for processing. Ordinary calculations
do not persist raw structures or images, but call metadata is retained under the
service policy (reference retention: 30 days). Clients and proxies may retain data
separately. Optional editor links temporarily store encrypted structures only after
explicit consent. Remote processing is not an absolute confidentiality guarantee.
See [privacy](docs/privacy.md) and the [official privacy policy](https://ai.freechemdraw.com/privacy).

## Documentation

- [Getting started](docs/getting-started.md)
- [Tools, parameters and result fields](docs/tools.md)
- [Remote MCP and client configuration](docs/remote-mcp.md)
- [REST API](docs/rest-api.md)
- [Usage limits and errors](docs/usage-limits.md)
- [Privacy](docs/privacy.md)
- [Troubleshooting](docs/troubleshooting.md)
- [FAQ](docs/faq.md)
- [Changelog](CHANGELOG.md)
- [Contributing](CONTRIBUTING.md)
- [Security reporting](SECURITY.md)

## Limitations

Name/CAS lookup is unavailable. Only the default depiction style is verified;
ACS-specific styling is unsupported. The optional editor integration is not a fifth
universally enabled core tool. Client-product certification, unlimited usage and
scientific validation of every molecule are not claimed. This repository does not
distribute the server implementation or grant a license to it.

## Issues and Contributions

Use [Issues](https://github.com/chemaiatlas/freechemdraw-mcp/issues) for documentation
corrections, synthetic bug reproductions and feature suggestions. Contributions
cover documentation and safe integration examples. See [contributing](CONTRIBUTING.md).
Report vulnerabilities privately as described in [SECURITY.md](SECURITY.md).
FreeChemDraw is the product brand; the repository is hosted by the ChemAI Atlas organization.
