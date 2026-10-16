# Data flow and privacy

> This repository provides documentation and integration examples for the FreeChemDraw hosted MCP service. The server implementation is privately maintained.

## Remote processing

Remote MCP and REST send submitted structures to the FreeChemDraw hosted service
for computation. Ordinary calculations do not persist raw structures or images,
and no external name/CAS provider is queried. This does not mean the service
keeps no records: call metadata includes tool, time, success/status, duration,
request identifier and attempt units. Reference retention is 30 days; effective
retention follows the published service policy and deployment settings.

The usage ledger does not retain raw structures, images, raw IPs, tokens or
authorization headers. Application request handling suppresses structure-bearing
request/exception diagnostics and discards chemical parser diagnostics. This is
not a promise that every infrastructure component has no logs. AI clients,
reverse proxies and infrastructure may apply their own retention policies.

Operational aggregate counters use date, predefined event/channel/locale and
counts; they are not unique-user metrics or paid credits. Account-related data
is separate from anonymous chemical calls; see the official policy.

## Optional editor link and consent

Use `get_editor_link` only when advertised, after explaining temporary storage
and receiving explicit user consent. `allow_temporary_storage=true` communicates
that consent. Structures are encrypted for one-time import; the link is a bearer
credential and expires after 10 minutes. Do not share a real link in issues, logs
or third-party services. Expired records are removed by periodic maintenance,
normally about 11 minutes after creation; downtime can delay physical removal.
The editor's own browser autosave settings still apply.

Without consent, do not call the optional tool. Copy SMILES or save the MOL result
and import it manually in [FreeChemDraw](https://freechemdraw.com/) instead.
That avoids creating a temporary editor-link record; earlier remote computation
still sent the structure to the hosted service. Neither this option nor remote
processing provides an absolute data security guarantee.

Read the [official privacy policy](https://ai.freechemdraw.com/privacy) and
[contact page](https://ai.freechemdraw.com/contact) for data requests. Use public,
non-confidential structures such as ethanol (`CCO`) in community bug reports.
