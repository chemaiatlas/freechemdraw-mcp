# Troubleshooting

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

| Symptom | Checks |
| --- | --- |
| Cannot resolve or reach the endpoint | Check DNS, HTTPS access and the current service status using normal certificate verification |
| Client cannot initialize | Use the exact `/mcp` URL and Streamable HTTP; let the client's MCP transport negotiate the protocol |
| Bare JSON POST to `/mcp` fails | Use a real MCP client; tool-call arguments alone are not the complete protocol request |
| HTTP 403 | Distinguish application Origin rejection from an upstream HTML challenge or edge policy; inspect the response type |
| HTTP 429 | Respect `Retry-After`, reduce polling and retry with backoff |
| `invalid_structure` | Check SMILES/MOL format and use a minimal public reproduction |
| `query_type_unavailable` | Name/CAS lookup is unavailable; use a SMILES query |
| `unsupported_style` | Use the default style; ACS-specific style is unsupported |
| `invalid_dimensions` | Use sides 64–2048 pixels and area at most 2,000,000 pixels |
| Image is a long string | Decode `data_base64`; it is not a download URL |
| Optional tool or route missing | Check discovery; editor links may be disabled |
| 503 / 504 | Follow `retryable`; reduce complexity and retry later when appropriate |

A previous public protocol check observed edge rejection for the default Python
urllib user-agent while browser/curl and other tested user-agent values succeeded.
That was a transport-level observation, not certification of actual SDK packages.
If affected, use the documented client transport, keep normal TLS validation and
report non-secret response metadata privately as appropriate; do not bypass service
security controls or assume all Python clients are unsupported.

For a public [issue](https://github.com/chemaiatlas/freechemdraw-mcp/issues), provide
client/version, HTTP status, domain code, non-secret request identifier and an
ethanol (`CCO`) reproduction. Omit credentials, cookies, editor links and private
structures. Vulnerabilities follow [private reporting](../SECURITY.md).
See [limits](usage-limits.md), [MCP](remote-mcp.md), and [REST](rest-api.md).
