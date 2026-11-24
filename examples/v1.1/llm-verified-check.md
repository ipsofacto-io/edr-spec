---
edr: 1
id: 01M87K0A1B2C3D4E5F6G7H8J9K
slug: prod-firewall-deny-default-egress
title: Production firewall policies deny default internet egress unless explicitly allowlisted
status: verified
owner: team:netsec
scope:
  - org: acme
    repo: acme/network-infra
    env: [prod]
valid_from: 2026-11-24
valid_to: null
review_by: 2027-05-24
supersedes: []
criticality: blocking
checks:
  - engine: llm
    target: json-snapshot
    ref: .ipso/checks/firewall-egress.llm.json
    mode: block
    max_age_days: 7
waivers: []
sources:
  - type: pr
    url: https://github.com/acme/network-infra/pull/142
tags: [security, firewall]
---

# Production firewall policies deny default internet egress unless explicitly allowlisted

## Context

Production VPCs hold regulated workloads whose outbound traffic must reach only approved endpoints. Firewall rules are exported nightly from live state as JSON snapshots whose vendor schemas vary across clouds.

## Decision

Production firewall snapshots must have a default-deny egress rule and must justify every `0.0.0.0/0` or `::/0` egress entry with an approved service tag. A live check runs at least once every 7 days.

## Alternatives considered

- Manual quarterly firewall reviews. Drift between reviews goes unnoticed for months.
- Cloud-specific Rego rules per firewall vendor. Vendor snapshot formats differ across accounts while the security invariant is shared.

## Consequences

Any snapshot with an unapproved wildcard egress rule fails the check, and a snapshot older than 7 days marks compliance stale.

## Verification

`.ipso/checks/firewall-egress.llm.json` carries approved allow and deny fixtures and requires a verbatim quote from the snapshot JSON for any violation.
