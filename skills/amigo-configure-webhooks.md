---
name: Configure and verify Amigo webhooks (Classic API)
description: Register a webhook destination, subscribe to events, verify HMAC signatures, rotate the signing secret, and inspect deliveries.
api: openapi/amigo-classic-openapi-original.json
operations:
  - create-webhook-destination
  - get_webhook_destinations
  - rotate-webhook-destination-secret
  - get-webhook-deliveries
---

# Configure and verify Amigo webhooks (Classic API)

Base URL: `https://api.amigo.ai`. Paths are organization-scoped under
`/v1/{organization}/webhook_destination/`.

## Steps

1. **Register a destination.** Call `create-webhook-destination`
   (`POST /v1/{organization}/webhook_destination/`) with your HTTPS endpoint and the event
   types you want: `api-key-expiration-soon`, `conversation-post-processing-complete`,
   `agent-framework-resource-updated`.
2. **List / confirm.** Verify with `get_webhook_destinations`
   (`GET /v1/{organization}/webhook_destination/`).
3. **Verify signatures.** On each delivery, validate the HMAC signature using the
   destination's signing secret before trusting the payload.
4. **Rotate the secret.** Periodically call `rotate-webhook-destination-secret`
   (`POST /v1/{organization}/webhook_destination/{webhook_destination_id}/rotate-secret`).
5. **Inspect deliveries.** Debug with `get-webhook-deliveries`
   (`GET /v1/{organization}/webhook_destination/{webhook_destination_id}/delivery`).

## Rules

- Always verify the HMAC signature; treat unsigned/invalid payloads as untrusted.
- Return 2xx quickly and process asynchronously; Amigo records delivery attempts.
- See `asyncapi/amigo-classic-webhooks.yml` for the full event + management catalog.
