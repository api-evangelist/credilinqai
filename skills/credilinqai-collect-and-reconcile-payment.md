---
name: Collect and reconcile a loan payment
description: Initiate a repayment, capture the payment, reconcile/verify it, and read payment history.
api: openapi/credilinqai-openapi.yml
method: generated
source: openapi/credilinqai-openapi.yml + https://docs.credilinq.ai/docs/
operations: [PaymentsController_initiatePaymentBNPLV1, PaymentsController_makePaymentBNPLV1, PaymentsController_verifyPaymentBNPL, PaymentsController_getAllPaymentsV1]
---

# Collect and reconcile a loan payment

Take a repayment against an existing drawdown/loan.

## Steps

1. Authenticate (see `credilinqai-authenticate.md`).
2. `PaymentsController_initiatePaymentBNPLV1` — `POST /v1/payments/loan/initiate-payment` to start a repayment for the loan.
3. `PaymentsController_makePaymentBNPLV1` — `POST /v1/payments/loan/make-payment` to capture the payment.
4. `PaymentsController_verifyPaymentBNPL` — `POST /v1/payments/loans/reconcilepayment` to reconcile/verify. Expect a `DRAWDOWN_PAYMENT_VERIFIED` webhook.
5. `PaymentsController_getAllPaymentsV1` — `GET /v1/payments/loans/payment/{loanId}` to read the full payment history.

## Rules

- Always reconcile after capture so ledger state matches; treat `DRAWDOWN_PAYMENT_VERIFIED` as the source of truth.
- Verify webhook signatures (HMAC SHA-256 over `<timestamp>.<body>` with your `client_secret`) before acting on them.
- On `404 E_D_NOT_FOUND`, re-check the `loanId`.
