# Tools, parameters and results

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

Discover enabled tools using `tools/list`. Inputs use snake_case. The four core
interfaces below share chemical behavior with the REST routes in [REST API](rest-api.md).
Only deployment discovery establishes which optional features are enabled.

## Core tool inputs

| Tool | Required input | Optional defaults |
| --- | --- | --- |
| `resolve_chemical` | `query` (SMILES) | `query_type=smiles`, `include_properties=false` |
| `convert_structure` | `structure` | `input_format=smiles`, `output_format=smiles`, `canonicalize=true` |
| `calculate_properties` | `structure` | `input_format=smiles`, `properties=null` (all six) |
| `render_structure` | `structure` | `input_format=smiles`, `output_format=svg`, `width=800`, `height=500`, `style=default` |

Structure inputs support `smiles` and `mol`; resolve accepts only SMILES queries.
Conversion outputs are `smiles` or `mol`. Rendering outputs are `svg` or `png`.
Supported stereochemistry is preserved; conversion from MOL to SMILES loses
coordinates and annotations, with a warning. Multiple fragments may produce warnings.
Name/CAS lookup is unavailable; ACS-specific rendering is unsupported.

## Core tool outputs

Successful MCP calls contain JSON `structuredContent` and matching JSON text content.
The four core results include `provider`, `engine_version`, and `warnings`.

| Tool | Result fields |
| --- | --- |
| `resolve_chemical` | `compound.smiles`, `compound.molecular_formula`, `status=ok`, `source=rdkit-input`, optional `properties` |
| `convert_structure` | `structure`, `format` |
| `calculate_properties` | `smiles`, `calculated=true`, `properties`, `units`, `methods` |
| `render_structure` | `mime_type`, `data_base64`, `width`, `height`, `style`, `rendering` |

Decode `data_base64` to obtain an SVG/PNG image. It is not a download URL.
Select one to six supported properties, or omit `properties` / set it to null for all six:

| Property | Unit / method |
| --- | --- |
| `molecular_formula` | molecular formula |
| `molecular_weight` | g/mol / atomic weights |
| `exact_mass` | Da / isotope masses |
| `formal_charge` | elementary charge / formal charge sum |
| `logp` | dimensionless / Wildman–Crippen |
| `tpsa` | square angstroms / topological polar surface area |

These are calculated properties, not experimental measurements.

## Examples

Use the payloads through an MCP client's `tools/call`, not as bare HTTP bodies:

```json
{"name":"resolve_chemical","arguments":{"query":"CCO","query_type":"smiles","include_properties":true}}
```

```json
{"name":"convert_structure","arguments":{"structure":"CCO","input_format":"smiles","output_format":"mol"}}
```

```json
{"name":"calculate_properties","arguments":{"structure":"CCO","properties":["molecular_formula","molecular_weight"]}}
```

```json
{"name":"render_structure","arguments":{"structure":"CCO","output_format":"png","width":400,"height":300}}
```

Downloadable request files: [properties](../examples/calculate-properties.json),
[conversion](../examples/convert-structure.json), [rendering](../examples/render-structure.json).

## Optional editor links

`get_editor_link` is available only when enabled and advertised by the deployment.
It is separate from the four core tools. Required: `structure` and explicit
`allow_temporary_storage=true`; defaults: `input_format=smiles`, `locale=en`.
Supported locale identifiers are `en`, `zh`, `ja`.

Only after the user consents to temporary encrypted storage:

```json
{"name":"get_editor_link","arguments":{"structure":"CCO","input_format":"smiles","locale":"en","allow_temporary_storage":true}}
```

The result contains `editor_url`, `expires_at`, `share_mode=temporary-one-time-token`
and `warnings`. The link is a bearer credential, valid for 10 minutes and usable
once. Do not paste a real link into a public issue. See [privacy](privacy.md).
If disabled, the tool is absent from discovery and its REST route is not advertised.

Domain errors set MCP `isError=true` with `error.code`, `message`, `http_status`
and `retryable`. Protocol/schema errors use JSON-RPC errors from the MCP stack.
See [limits and errors](usage-limits.md).
