---
edr: 1
id: 01M87K0A1B2C3D4E5F6G7H8J9M
slug: k8s-ingress-tls-freshness
title: Production Kubernetes Ingress manifests require TLS and daily live verification
status: verified
owner: team:platform
scope:
  - org: acme
    repo: acme/cluster-manifests
    env: [prod]
valid_from: 2026-11-24
valid_to: null
review_by: 2027-05-24
supersedes: []
criticality: blocking
checks:
  - engine: kyverno
    target: kubernetes-manifest
    ref: policies/ingress-tls.yaml
    mode: block
    max_age_days: 2
waivers: []
sources:
  - type: pr
    url: https://github.com/acme/cluster-manifests/pull/88
tags: [kubernetes, tls]
---

# Production Kubernetes Ingress manifests require TLS and daily live verification

## Context

Live cluster Ingress objects can be patched directly during incidents and drift from Git manifests if scheduled checks stop running.

## Decision

Every production Ingress object must configure `spec.tls`, and its live check verdict must be no older than 2 days.

## Alternatives considered

- Rely only on pull-request checks. Direct `kubectl` edits in emergencies bypass pull requests.

## Consequences

If the scheduled cluster check stops reporting for more than 2 days, decision health reports compliance as stale instead of compliant.

## Verification

`ipso check kyverno` evaluates exported cluster Ingress manifests daily and uploads live verdicts.
