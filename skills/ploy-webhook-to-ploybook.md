---
name: Send an event to Ploy and trigger a Ploybook
description: POST a JSON event to a Ploy inbound webhook endpoint with a bearer key so it is stored and runs a wired Ploybook, handling every documented response code correctly.
api: POST https://ploy.ai/api/v1/webhook/{endpointSlug}
generated: '2026-08-12'
method: generated
source: https://docs.ploy.ai/webhooks
operations:
  - POST /api/v1/webhook/{endpointSlug}
---

# Send an event into Ploy

This is the only public HTTP endpoint Ploy documents. It is **inbound** — you
push events to Ploy; Ploy does not deliver events to you. An accepted event is
stored verbatim and, if the endpoint has a Ploybook trigger wired, runs that
Ploybook with the payload injected as prompt context.

## Prerequisites

Create the endpoint in Workspace Settings → Webhooks. Ploy shows the ingest URL
and the API key **once** — the key cannot be retrieved later. Keys are scoped to
a single endpoint.

## The call

```sh
curl -X POST https://ploy.ai/api/v1/webhook/ENDPOINT_SLUG \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "event": "lead.enriched",
    "email": "alex@acme.com",
    "company": "Acme Inc.",
    "title": "VP Marketing"
  }'
```

Contract:

| Field | Requirement |
|---|---|
| Method | `POST` only (a `GET` returns 405) |
| Content-Type | `application/json` only |
| Body | any well-formed JSON object — Ploy defines no schema and stores it verbatim |
| Payload size | 1 MB max |
| Auth | `Authorization: Bearer {apiKey}` on every request |

## Handling the response

| Status | Meaning | What to do |
|---|---|---|
| 202 | Stored; Ploybook queued asynchronously | done — do not resend |
| 400 | Body is not valid JSON | fix the body; do not retry as-is |
| 401 | Missing/invalid `Authorization` | check for exactly `Bearer <key>`, one space, no quotes |
| 403 | Endpoint paused or disabled | re-enable in Settings → Webhooks |
| 404 | Endpoint slug does not exist | confirm the URL on the endpoint detail page |
| 413 | Body over 1 MB | send one event per row, do not batch |
| 415 | Wrong Content-Type | set `application/json` (form/multipart rejected) |
| 429 | Rate limited | back off and retry with jitter |

Error bodies are a bare JSON envelope, not RFC 9457: `{"error":"Endpoint not
found"}`.

## Retry and duplication rules

- **Ploy never retries an inbound event.** Retry is entirely the sender's job:
  retry on 5xx and 429 with exponential backoff and jitter; do not retry any
  other 4xx.
- **There is no idempotency key and no dedupe.** A resent event is stored again
  and runs the wired Ploybook again. If the Ploybook publishes a page, a
  duplicate send can publish twice — dedupe on your side before sending.
- Ploybook execution happens after the 202. Failures do not surface in the HTTP
  response; check Settings → Webhooks → Events for the run status.

## Verifying

Browse Workspace Settings → Webhooks → Events and filter by endpoint, time range
or a payload substring to confirm the event landed and the Ploybook ran.
