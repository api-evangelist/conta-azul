---
name: Look up a product and create a sale
description: Find a product in Conta Azul inventory, create a sale for it, read it back, and print the PDF.
api: openapi/conta-azul-sales-openapi.yml
operations: [getProductsByFilter, createVenda, getVendaById, printVendaPdf]
---

# Look up a product and create a sale

Base URL: `https://api-v2.contaazul.com`. Send `Authorization: Bearer <access_token>` on every
call (see `authentication/conta-azul-authentication.yml`). Portuguese domain: venda=sale,
produto=product, vendedor=salesperson.

## Steps

1. **Find the product** — `GET /v1/produtos` (`getProductsByFilter`, in
   `openapi/conta-azul-inventory-openapi.yml`), filter by name/status and paginate with
   `pagina` + `tamanho_pagina` to get the product id.
2. **Create the sale** — `POST` create-sale (`createVenda`) with the customer, items (using the
   product id from step 1), and optionally `id_vendedor` to attribute the salesperson.
3. **Read it back** — `GET` sale-by-id (`getVendaById`) to confirm totals and status; use
   `getVendaItens` if you need the line items.
4. **Print** — `GET` print-sale-PDF (`printVendaPdf`) to retrieve the sale document.

## Rules

- Use `getNextVendaNumber` if you need to pre-allocate the sale number.
- Refresh the token on 401; back off on 429. Errors are plain JSON
  (`errors/conta-azul-problem-types.yml`).
