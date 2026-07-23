---
name: Run EV smart-charging flexibility for an asset
description: Register an EV/charger asset, report plug-in/out and intent events, and read the optimised price curve.
api: openapi/axle-energy-openapi-original.json
operations:
  - create_asset_entities_asset_post
  - asset_plug_in_event_entities_asset__asset_id__event_plug_in_post
  - asset_intent_update_event_entities_asset__asset_id__event_intent_post
  - get_price_curve_entities_asset__asset_id__price_curve_get
  - asset_plug_out_event_entities_asset__asset_id__event_plug_out_post
---

# Run EV smart-charging flexibility for an asset

Use this flow to enrol an EV charger for smart-charging flexibility and drive it with real-time events.

## Steps

1. **Authenticate.** Obtain a bearer token from POST `/auth/token-form` (`login_for_access_token_form_auth_token_form_post`).
2. **Create the asset.** POST `/entities/asset` (`create_asset_entities_asset_post`) to register the EV/charger under a site, carrying your `external_id`.
3. **Report plug-in.** POST `/entities/asset/{asset_id}/event/plug-in` (`asset_plug_in_event_entities_asset__asset_id__event_plug_in_post`) when the vehicle plugs in so Axle can optimise the session. For Flex Lite you can submit a schedule via `/event/plug-in-schedule` and receive a modified schedule back.
4. **Update charging intent.** POST `/entities/asset/{asset_id}/event/intent` (`asset_intent_update_event_entities_asset__asset_id__event_intent_post`) when the user changes their departure/charge intent.
5. **Read the price curve.** GET `/entities/asset/{asset_id}/price-curve` (`get_price_curve_entities_asset__asset_id__price_curve_get`) for half-hourly prices per MWh of flexed energy to drive optimisation (today until 23:00 UK; next-day from 14:00 UK).
6. **Report plug-out.** POST `/entities/asset/{asset_id}/event/plug-out` (`asset_plug_out_event_entities_asset__asset_id__event_plug_out_post`) when the vehicle unplugs.

## Rules

- Send telemetry via POST `/data/readings` (`post_readings_data_readings_post`) so Axle can measure baseline and flexed load.
- Tokens expire after 1 hour; refresh proactively. Handle `422`/`409` per `errors/axle-energy-problem-types.yml`.
