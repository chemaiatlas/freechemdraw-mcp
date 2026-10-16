# Chemical REST API

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

## Discovery and request format

Base URL: `https://ai.freechemdraw.com`.
Read [OpenAPI](https://ai.freechemdraw.com/openapi/v1.json) and
[capabilities](https://ai.freechemdraw.com/api/v1/capabilities) for the current deployment.
Basic chemical requests use anonymous, free, rate-limited access under the public
contract; account registration and an API Key are not required.

Send POST requests with `Content-Type: application/json`. Use the JSON arguments
as the REST body, without MCP `name` / `arguments` or JSON-RPC framing. Unknown or
unrelated fields are rejected. Field names use snake_case. Use `structure` and
`input_format`, not an undocumented `smiles` field. Resolve uses `query`.

| REST POST route | Equivalent tool |
| --- | --- |
| `/api/v1/chemicals/resolve` | `resolve_chemical` |
| `/api/v1/structures/convert` | `convert_structure` |
| `/api/v1/properties/calculate` | `calculate_properties` |
| `/api/v1/structures/render` | `render_structure` |

## Ethanol requests

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/chemicals/resolve -H 'Content-Type: application/json' --data '{"query":"CCO","query_type":"smiles"}'
```

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/structures/convert -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles","output_format":"mol"}'
```

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles"}'
```

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/structures/render -H 'Content-Type: application/json' --data '{"structure":"CCO","output_format":"svg","width":400,"height":300}'
```

Use `output_format=png` for PNG. The [REST properties body](../examples/rest-calculate-properties.json)
can also be sent directly using curl's file-body option. The other JSON request
files use MCP tool-call payloads and must not be sent unchanged to REST.

## Results and errors

Success: HTTP 200, `request_id`, `data`, `error=null`, `usage.calls=1`.
`data` follows the [tool-specific result fields](tools.md).
Failure: the HTTP status is retained, `data=null`, `usage.calls=0`, and `error`
contains `code`, `message` and `retryable`. Call counts are not paid credits.
Keep the non-secret request identifier for support; avoid disclosing structures.
No sample result here is presented as a live response.

HTTP 429 includes `Retry-After`. Respect it, then retry with backoff. See
[error codes](usage-limits.md). A 403 may be an Origin policy rejection or an
upstream edge restriction; do not assume every 403 is an authentication failure.
Cross-origin browser chemical requests are rejected; a same-origin application
or a non-browser integration avoids that particular browser-origin mismatch.

## Optional editor integration

Only when enabled, `/api/v1/editor-links` corresponds to `get_editor_link`.
It requires explicit consent to temporary encrypted storage and returns a
single-use, 10-minute editor link. Disabled deployments omit the route from
OpenAPI. See [tools](tools.md) and [privacy](privacy.md); do not create links
without the user's consent. Editor redemption and import are governed by the
platform's configured editor origin and are not generic cross-origin permission.
