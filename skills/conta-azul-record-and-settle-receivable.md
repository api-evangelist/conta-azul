---
name: Record a receivable and settle it
description: Create an accounts-receivable financial event in Conta Azul, find its installment, and register the settlement (baixa).
api: openapi/conta-azul-financial-openapi.yml
operations: [createReceivableFinancialEvent, searchInstallmentsToReceiveByFilter, criarBaixa]
---

# Record a receivable and settle it

Base URL: `https://api-v2.contaazul.com`. All calls send `Authorization: Bearer <access_token>`
(OAuth 2.0 Authorization Code — see `authentication/conta-azul-authentication.yml`). Resource
names and fields are in Brazilian Portuguese.

## Steps

1. **Create the receivable** — `POST /v1/financeiro/eventos-financeiros/contas-a-receber`
   (`createReceivableFinancialEvent`). Send the competência date, valor, descrição, conta
   financeira, and condições de pagamento (parcelas). Optionally include `metodo_pagamento`
   inside each installment.
2. **Locate the installment** — `GET /v1/financeiro/eventos-financeiros/contas-a-receber/buscar`
   (`searchInstallmentsToReceiveByFilter`), filtering by due date / status / valor to get the
   installment id created in step 1. Paginate with `pagina` + `tamanho_pagina`.
3. **Settle it (baixa)** — `POST` the acquittance endpoint (`criarBaixa`, in
   `openapi/conta-azul-acquittance-openapi.yml`) referencing the installment to register the
   payment/settlement.

## Rules

- The access token expires after 3600s — refresh via the OAuth2 token endpoint on 401.
- Errors return plain `application/json` (not problem+json); see
  `errors/conta-azul-problem-types.yml`. Back off on `429`.
- No idempotency-key is supported — do not blindly retry a `POST` that may have partially
  succeeded; re-query with the search endpoint first.
