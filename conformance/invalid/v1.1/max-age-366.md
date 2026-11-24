---
edr: 1
id: 01M87K0A1B2C3D4E5F6G7H8J9R
slug: check-max-age-366
title: A check with max_age_days above 365 is invalid
status: accepted
owner: team:platform
scope:
  - org: acme
valid_from: 2026-11-24
valid_to: null
review_by: 2027-05-24
supersedes: []
criticality: warning
checks:
  - engine: opa
    ref: policies/rule.rego
    max_age_days: 366
waivers: []
sources:
  - type: pr
    url: https://github.com/acme/app/pull/4
tags: [opa]
---

# A check with max_age_days above 365 is invalid

## Context

Context text.

## Decision

Decision text.

## Alternatives considered

- Alternative text.

## Consequences

Consequences text.

## Verification

Verification text.
