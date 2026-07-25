---
name: Onboard a customer and complete KYC
description: Check eligibility, run data processing, capture customer and director KYC, and send it for review.
api: openapi/credilinqai-openapi.yml
method: generated
source: openapi/credilinqai-openapi.yml + https://docs.credilinq.ai/docs/
operations: [OnboardingController_initiateOnboardingApplication, OnboardingController_initiateDaraProcessing, KycController_createCustomerKYCV2, KycController_addDirectorKycV2, KycController_sendKycForReview, KycController_getKycData, OnboardingController_generateKycDocusign]
---

# Onboard a customer and complete KYC

Bring a new SME customer onto the CrediLinq embedded-finance flow.

## Steps

1. Authenticate (see `credilinqai-authenticate.md`).
2. `OnboardingController_initiateOnboardingApplication` — `POST /v1/onboarding/check-eligibility` to start an application and check eligibility. Keep the returned `applicationId`.
3. `OnboardingController_initiateDaraProcessing` — `POST /v1/onboarding/initiate-data-processing` to run credit/data processing. Wait for the `DATA_PROCESSING_COMPLETED` (or `DATA_PROCESSING_FAILED`) webhook.
4. `KycController_createCustomerKYCV2` — `POST /v2/kyc/customer/create` to submit business KYC for the application.
5. `KycController_addDirectorKycV2` — `POST /v2/kyc/director/create` for each director. (For an individual borrower use `KycController_createIndividualCustomerKYC`.)
6. `KycController_sendKycForReview` — `POST /v1/kyc/send-for-review` to submit KYC.
7. `OnboardingController_generateKycDocusign` — `POST /v1/onboarding/send-agreement` to send the loan agreement for signature.
8. Poll `KycController_getKycData` — `GET /v1/kyc/details/{applicationId}` or wait for `CUSTOMER_APP_ACCEPTED` / `CUSTOMER_APP_REJECTED` webhooks.

## Rules

- All operations require the Bearer token.
- KYC create/update for customer and director are on the `v2` path prefix; send-for-review and details are on `v1`.
- On `404 E_O_APPLICATION_NOT_FOUND`, re-check the `applicationId`.
