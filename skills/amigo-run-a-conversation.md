---
name: Run an Amigo agent conversation (Classic API)
description: Authenticate, open a conversation with an Amigo service/agent, exchange turns, read messages, and finish the conversation.
api: openapi/amigo-classic-openapi-original.json
operations:
  - sign-in-with-api-key
  - create-conversation
  - interact-with-conversation
  - get-conversation-messages
  - finish-conversation
---

# Run an Amigo agent conversation (Classic API)

Base URL: `https://api.amigo.ai` (regional hosts: `api-eu-central-1`, `api-ap-southeast-2`, `api-ca-central-1`). All paths are organization-scoped under `/v1/{organization}/`.

## Steps

1. **Authenticate.** Exchange your API key for a user JWT with `sign-in-with-api-key`
   (`POST /v1/{organization}/user/signin_with_api_key`). Send the returned token as
   `Authorization: Bearer <jwt>` on every subsequent call. Set `X-ORG-ID` only for
   cross-organization requests.
2. **Create a conversation.** Call `create-conversation`
   (`POST /v1/{organization}/conversation/`) with the target service to start a session.
3. **Exchange turns.** Drive the dialog with `interact-with-conversation`
   (`POST /v1/{organization}/conversation/{conversation_id}/interact`), streaming user
   input and reading agent responses.
4. **Read history.** Retrieve the transcript with `get-conversation-messages`
   (`GET /v1/{organization}/conversation/{conversation_id}/messages/`).
5. **Finish.** Close the session with `finish-conversation`
   (`POST /v1/{organization}/conversation/{conversation_id}/finish/`) so
   post-processing runs (a `conversation-post-processing-complete` webhook fires when done).

## Rules

- Auth: organization-scoped JWT bearer (`conventions/amigo-conventions.yml`).
- Errors: 422 returns `HTTPValidationError {detail:[{loc,msg,type}]}`; back off on 429
  (`errors/amigo-problem-types.yml`).
- Prefer the official SDKs (`amigo_sdk` Python, `@amigo-ai/sdk` npm) for streaming + retry.
