---
edr: 1
id: 01M87K0A1B2C3D4E5F6G7H8J9V
slug: llm-check-encoded-ref
title: An llm check with percent-encoded traversal in ref is invalid
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
  - engine: llm
    ref: "%2e%2e/outside/check.llm.json"
waivers: []
sources:
  - type: pr
    url: https://github.com/acme/app/pull/6
tags: [llm]
---

# An llm check with percent-encoded traversal in ref is invalid

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
