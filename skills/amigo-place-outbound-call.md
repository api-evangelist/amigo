---
name: Place an outbound voice call (Amigo Platform API)
description: Mint a workspace API key, provision access, and place an idempotent outbound voice call driven by an Amigo agent.
api: openapi/amigo-platform-openapi-original.json
operations:
  - create-api-key
  - get-api-key-permission-catalog
  - create-outbound-call
  - list_active_sessions_v1__workspace_id__sessions_active_get
---

# Place an outbound voice call (Amigo Platform API)

Base URL: `https://api.platform.amigo.ai`. All paths are workspace-scoped under `/v1/{workspace_id}/`.

## Steps

1. **Get credentials.** Either mint a workspace-scoped key with `create-api-key`
   (`POST /v1/{workspace_id}/api-keys`) and send it as `Authorization: Bearer <api_key>`,
   or obtain an OAuth2 access token via client_credentials at
   `https://identity.platform.amigo.ai/token` (scopes in `scopes/amigo-scopes.yml`;
   `calls:initiate` is required to place calls).
2. **Check permissions.** Confirm the key's grants with `get-api-key-permission-catalog`
   (`GET /v1/{workspace_id}/api-keys/permission-catalog`).
3. **Place the call.** Call `create-outbound-call`
   (`POST /v1/{workspace_id}/calls/outbound`). Supply an `idempotency_key` (max 256 chars)
   so a retried request never dials twice.
4. **Track the session.** Poll `list_active_sessions...` (`GET /v1/{workspace_id}/sessions/active`)
   to observe the live call session.

## Rules

- Idempotency: always send `idempotency_key` on `create-outbound-call`
  (`conventions/amigo-conventions.yml`).
- Auth scopes: `calls:initiate`, `calls:read` (`scopes/amigo-scopes.yml`).
- Errors: back off on 429; 422 is `HTTPValidationError` (`errors/amigo-problem-types.yml`).
- SDK: `@amigo-ai/platform-sdk` (npm).
