---
name: Issue a charge and check its status
description: Create a charge (cobrança) in Conta Azul and poll its status by id.
api: openapi/conta-azul-charge-openapi.yml
operations: [criarCobranca, buscarCobrancaPorId]
---

# Issue a charge and check its status

Base URL: `https://api-v2.contaazul.com`. Send `Authorization: Bearer <access_token>` on every
call (see `authentication/conta-azul-authentication.yml`). Portuguese: cobrança=charge.

## Steps

1. **Create the charge** — `POST` create-charge (`criarCobranca`) with the payer, amount, and
   due date. Capture the returned charge id.
2. **Check status** — `GET` charge-by-id (`buscarCobrancaPorId`) to read the current status.
   Poll with backoff rather than tight-looping.
3. **Cancel if needed** — `DELETE` charge-by-id (`deletarCobrancaPorId`).

## Rules

- The access token expires after 3600s — refresh on 401.
- Errors return plain `application/json`; see `errors/conta-azul-problem-types.yml`. Respect
  `429` with exponential backoff.
- No idempotency key — re-query by id before retrying a create.
