---
name: Create and disburse a loan (drawdown)
description: Preview a repayment schedule, create a BNPL drawdown against a credit line, and disburse it.
api: openapi/credilinqai-openapi.yml
method: generated
source: openapi/credilinqai-openapi.yml + https://docs.credilinq.ai/docs/
operations: [ClientController_getCustomerCreditLineDetails, DrawdownController_calculateRepaymentScheduleBNPL, DrawdownController_createDrawdownBNPL, DrawdownController_disburseDrawdownBNPL, DrawdownController_getDrawdownBNPL]
---

# Create and disburse a loan (drawdown)

Draw down financing for an approved customer.

## Steps

1. Authenticate (see `credilinqai-authenticate.md`).
2. `ClientController_getCustomerCreditLineDetails` — `GET /v1/customer/{customerReferenceNo}/line-details` to confirm available credit.
3. `DrawdownController_calculateRepaymentScheduleBNPL` — `POST /v1/loan/calculateloanschedule` to preview the repayment schedule for the requested amount/tenure.
4. `DrawdownController_createDrawdownBNPL` — `POST /v1/loan` to create the drawdown. Keep the returned `loanId`. Expect a `DRAWDOWN_INITIATED` webhook, then `DRAWDOWN_APPROVED`.
5. `DrawdownController_disburseDrawdownBNPL` — `POST /v1/loan/disburse` to disburse. Expect a `DRAWDOWN_DISBURSED` webhook.
6. `DrawdownController_getDrawdownBNPL` — `GET /v1/loan/summary/{loanId}` to read the loan summary/status.

## Rules

- Confirm the credit line before creating a drawdown to avoid `400` on limit overrun.
- On `404 E_D_NOT_FOUND`, re-check the `loanId`.
- No idempotency key is supported; do not blindly retry a create — check `getDrawdownBNPL` first.
