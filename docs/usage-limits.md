# Free usage limits and errors

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

Basic chemical processing is free and anonymous under the public contract.
There are no paid credits, subscriptions, issued API Keys or account-based chemical
quota upgrades in that contract. Usage counters measure calls or attempts, not money.

## Reference limits

These are the current documented reference defaults and fixed input boundaries,
not a claim that every production configuration has been independently measured.
Check the [official service documentation](https://ai.freechemdraw.com/docs/).

| Limit | Reference value |
| --- | --- |
| HTTP per IP per minute | 20 |
| HTTP per server process per minute | 120 |
| Concurrent chemical operations | 2 |
| Operation timeout | 10 seconds |
| Atoms per structure | 256 |
| SMILES length | 16,384 characters |
| MOL length | 262,144 characters |
| HTTP body | 1 MiB |
| Render sides | 64–2048 pixels |
| Render area | At most 2,000,000 pixels |

Initialization, discovery and other covered chemical HTTP requests consume the
shared HTTP budget. Do not poll tool discovery repeatedly. Capacity is bounded;
HTTP 429 may reflect the request budget or busy chemical processing. Follow
`Retry-After` when present and retry with exponential backoff. Free access has no SLA.

## Error reference

| HTTP | Domain codes | Action |
| --- | --- | --- |
| 400 | `invalid_request`, `invalid_input`, `invalid_structure` | Correct the request or structure |
| 400 | `unsupported_format`, `unsupported_property`, `unsupported_style`, `invalid_dimensions` | Use supported arguments |
| 400 | `query_type_unavailable`, `structure_too_complex`, `invalid_locale` | Use SMILES, simplify the structure or correct the locale |
| 400 | `storage_consent_required` | Obtain consent or use manual import |
| 403 | `origin_denied` | Use a permitted origin |
| 404 / 405 | `not_found` / `method_not_allowed` | Check route and method |
| 410 | `editor_link_expired` | Request a new link only after consent |
| 413 / 415 | `request_too_large`, `output_too_large` / `unsupported_media_type` | Reduce size or send JSON |
| 429 | `rate_limited`, `engine_busy` | Wait and retry |
| 500 / 502 | `internal_error` / `engine_invalid_response` | Provide a non-secret request identifier to support |
| 503 | `engine_unavailable`, `engine_version_mismatch`, `editor_links_disabled` | Check availability |
| 504 | `engine_timeout` | Simplify the structure or retry later |

REST errors contain `error.code`, `message` and `retryable` in the response envelope.
MCP domain failures set `isError=true` with `error.code`, `message`, `http_status`
and `retryable`. MCP protocol/schema failures use JSON-RPC errors. Inspect
`retryable` rather than retrying every error blindly. Edge-generated HTTP errors
may not have the application JSON envelope. See [troubleshooting](troubleshooting.md).
