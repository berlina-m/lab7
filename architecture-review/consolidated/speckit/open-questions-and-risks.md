# Open questions, risks, and review checklist — Spec Kit

## Human decisions required

Decide supported currencies, minor-unit scales, accepted input scale, rounding mode and boundary, percentage precision/residual allocation; approval roles, self-approval, reviewer count/quorum, concurrency, rejection/resubmission; former-member access and retention; identity/recovery policy; receipt formats/limits/validation/retention; notification channels/preferences/retry/deduplication; report timezone/date boundaries/currency aggregation/snapshot/export; persistence technology; and whether a balance projection is operationally required.

## Findings addressed

The proposal makes payer-in/out accounting auditable with two worked examples, records ordered participants and residual policy, establishes server-side enforcement for all channels, separates Swing MVP from full scope, defines approval/payment transaction boundaries and idempotency, uses append-only corrections, separates retention from authorization, and treats notification failure independently from financial commit. These were critical/high review themes; unresolved policy choices remain explicitly open.

## Validation limits

The source requirements, constitution, Draft specification, checklist, plan, data model, original proposals, and ChatGPT review package were read as inputs and are not modified. PlantUML syntax validation was not run because no validator was available. No implementation code, builds, tests, deployment, or security implementation were generated or validated. Human review is required.

