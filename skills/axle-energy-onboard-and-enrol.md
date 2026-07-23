---
name: Onboard a site and enrol it in a flexibility market
description: Authenticate, then register a site and its assets and enrol them in a flex proposition in one idempotent call.
api: openapi/axle-energy-openapi-original.json
operations:
  - login_for_access_token_form_auth_token_form_post
  - check_eligibility_entities_site_check_eligibility_post
  - onboard_entities_site_onboard_post
  - get_site_eligibility_entities_site__site_id__eligibility_get
  - get_site_flex_events_entities_site__site_id__flex_events_get
---

# Onboard a site and enrol it in a flexibility market

Use this flow to bring a customer's site and its flexible assets onto the Axle platform and enrol them in a flex market proposition.

## Steps

1. **Authenticate.** POST `/auth/token-form` (`login_for_access_token_form_auth_token_form_post`) with your `username` and `password` as form data. Use the returned bearer token as `Authorization: Bearer <token>` on every call. Organisation tokens expire after 1 hour — refresh when needed.
2. **(Optional) Check eligibility.** POST `/entities/site/check-eligibility` (`check_eligibility_entities_site_check_eligibility_post`) to confirm which propositions a prospective site/asset can enrol in before onboarding.
3. **Onboard in one call.** POST `/entities/site/onboard` (`onboard_entities_site_onboard_post`) to upsert the site and its assets and enrol them in the relevant proposition atomically. This call is idempotent — sending the same site/asset (keyed by `external_id`) returns the existing record with new fields merged; if enrolment fails nothing is stored, so it is safe to retry. Prefer this over the deprecated `/onboard-site-and-asset`.
4. **Confirm eligibility detail.** GET `/entities/site/{site_id}/eligibility` (`get_site_eligibility_entities_site__site_id__eligibility_get`) to read the per-asset eligibility breakdown after onboarding.
5. **Track earnings.** GET `/entities/site/{site_id}/flex-events` (`get_site_flex_events_entities_site__site_id__flex_events_get`) to see the flex events the site participated in with estimated and final gross revenue.

## Rules

- Address sites and assets by your own `external_id` so retries stay idempotent.
- Handle `422` by inspecting the `detail[]` array (`loc`/`msg`/`type`); `409` means a conflicting state (already enrolled/consented).
- See `conventions/axle-energy-conventions.yml` and `errors/axle-energy-problem-types.yml`.
