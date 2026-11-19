---
edr: 1
id: 01M7SRHHME3FXR844TXRRZSGBS
slug: no-open-lodash-advisory
title: The lodash advisory SNYK-JS-LODASH-590103 stays closed
status: accepted
owner: team:security
scope:
  - org: acme
valid_from: 2026-11-01
valid_to: null
review_by: 2027-05-01
supersedes: []
criticality: blocking
checks:
  - engine: snyk
    ref: SNYK-JS-LODASH-590103
    mode: block
waivers: []
sources:
  - type: spec
    url: https://github.com/ipsofacto-io/edr-spec/blob/main/spec/edr-v0.2.md
tags: [security]
---

# The lodash advisory SNYK-JS-LODASH-590103 stays closed

## Context

A dependency advisory stays open until the lockfile moves past it. The decision names that advisory so a later scan can prove it is gone.

## Decision

`SNYK-JS-LODASH-590103` is not an open finding. The evidence file lists open Snyk ids, and this id must be absent from a scan that finished.

## Alternatives considered

- Ignore advisories below a severity. A low score still names a package the decision called out.
- Treat a missing id as a scan that did not run. A finished scan with an empty list is clean.

## Consequences

The check fails while the advisory is open. A scan that did not finish is an error, and a waiver does not cover that error.

## Verification

The evidence file for a finished scan has `tool` `snyk`, `status` `success`, and one object per open id. This id is absent from that list.
