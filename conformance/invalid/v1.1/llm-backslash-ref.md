---
edr: 1
id: 01M87K0A1B2C3D4E5F6G7H8J9U
slug: llm-check-backslash-ref
title: An llm check with backslashes in ref is invalid
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
    ref: "..\\outside\\check.llm.json"
waivers: []
sources:
  - type: pr
    url: https://github.com/acme/app/pull/5
tags: [llm]
---

# An llm check with backslashes in ref is invalid

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
