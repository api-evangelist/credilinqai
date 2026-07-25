---
name: Authenticate with CrediLinq
description: Obtain an Auth0 Bearer JWT access token and use it on all CrediLinq API calls.
api: openapi/credilinqai-openapi.yml
method: generated
source: openapi/credilinqai-openapi.yml + https://docs.credilinq.ai/docs/authentication-1.md
operations: [AuthenticationController_generateToken]
---

# Authenticate with CrediLinq

CrediLinq uses Auth0 client-credentials authentication. The CrediLinq support team issues a `client_id` / `client_secret` per environment (Sandbox, Staging, Production).

## Steps

1. Pick the environment base URL:
   - Sandbox: `https://sandbox-api.credilinq.ai`
   - Staging: `https://stage-api.credilinq.ai`
   - Production: `https://api.credilinq.ai`
2. Call `AuthenticationController_generateToken` — `POST /v1/auth/generate-token` with a JSON body of `{ "client_id": "...", "client_secret": "..." }`.
3. Read `access_token` (a JWT), `token_type` (`Bearer`), and `expires_in` (default 8 hours) from the response.
4. Send `Authorization: Bearer <access_token>` on every subsequent request.
5. Cache the token until it expires; only re-generate when expired.

## Rules

- Never expose `client_secret` client-side; the same secret is also the webhook signing secret.
- On `401 E_UNAUTHORIZED`, generate a fresh token and retry.
- Optionally set `accept-language` (`en-us` or `in`) to localize responses.
