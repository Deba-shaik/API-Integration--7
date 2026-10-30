# Topic 7 - Working with Webhooks and Event-Driven APIs

## Overview
This n8n hands-on workflow receives lead data through an HTTPS POST webhook, authenticates requests using a fixed secret in the `X-Webhook-Signature` header, maps lead fields, and supports both test and production webhook routes.

## Workflow
`Postman -> Webhook (POST /receive-leads) -> Extract Lead Details (Edit Fields)`

## Node configuration
### Webhook
- HTTP Method: POST
- Path: `receive-leads`
- Authentication: Header Auth credential using `X-Webhook-Signature` (secret not included in documentation)
- Respond: Immediately
- Test route: `/webhook-test/receive-leads`
- Production route: `/webhook/receive-leads`

### Extract Lead Details (Edit Fields)
- `leadId` (String): `{{ $json.body.leadId }}`
- `name` (String): `{{ $json.body.name }}`
- `email` (String): `{{ $json.body.email }}`
- `receivedAt` (String): `{{ $now.toISO() }}`

## Test request
POST to the Test URL or Production URL with headers `Content-Type: application/json` and `X-Webhook-Signature: <your-secret>`.

```json
{
  "leadId": "L-7788",
  "name": "Sarah Wilson",
  "email": "sarah@example.com"
}
```

## Verified tests (screenshots captured 30 October 2026)
| ID | Scenario | Observed result | Status |
| --- | --- | --- | --- |
| T01 | Test webhook POST with sample lead | Webhook received JSON body | PASS |
| T02 | Authenticated test request | Postman 200 OK; n8n received payload | PASS |
| T03 | Extract lead details | leadId, name, email, receivedAt populated | PASS |
| T04 | Missing authentication header | Postman 403 Forbidden | PASS |
| T05 | Production webhook authenticated POST | Postman 200 OK, "Workflow was started" | PASS |
| T06 | Workflow execution | Both Webhook and Extract Lead Details show successful execution, 1 item | PASS (execution mode not shown) |

## Limitations and security
- The guide discusses HMAC verification. This implementation demonstrates **fixed-secret Header Auth**, not cryptographic HMAC-SHA256 calculation and comparison. Do not claim HMAC verification has been completed.
- A successful production endpoint response is verified; the final canvas screenshot does not independently display its execution mode.
- The original shared secret appeared in screenshots. Rotate the credential before any real deployment; never commit it to the README or JSON export.
- ngrok was unnecessary for this hosted HTTPS n8n instance. It would be required for an inaccessible local-only instance.

## Submission
Include this README, the evidence PDF, exported n8n workflow JSON (without credentials), and a short walkthrough video. In the video, show valid and missing-header Postman requests, the workflow execution, and the lead mapping. Avoid exposing secrets.
