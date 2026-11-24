# EDR v1.1

- Status: Stable
- Version: 1.1.0
- Date: 2026-11-24
- License: CC-BY-4.0 (this text). The JSON Schema and the files under `examples/` and `conformance/` are Apache-2.0.

This document is the normative specification of the Engineering Decision Record (EDR) format at version 1.1.0. An EDR file records one engineering decision: who owns it, where it applies, when it is in force, which check verifies it, how fresh its verdicts must stay, and which premises it rests on. The file is Markdown with YAML frontmatter, and it is a superset of a MADR record.

The key `edr` is the format generation. Every 0.x draft and every 1.x version uses `edr: 1`, and a major version 2 would use `edr: 2`. That number is not the SemVer of this document. Section 6 states the versioning rules.

## 1. Artifacts

| Artifact | Location |
| --- | --- |
| This text | `spec/edr-v1.1.md` |
| JSON Schema | `schema/edr-v1.1.schema.json` |
| Positive examples | `examples/`, `examples/v0.2/`, and `examples/v1.1/` |
| Negative fixtures | `conformance/invalid/`, `conformance/invalid/v0.2/`, and `conformance/invalid/v1.1/` |

The schema `$id` is `https://ipsoplatform.io/schema/edr/v1.1.json`. That URI is the canonical name of this schema file and of no other file. Version 1.0.0 (`spec/edr-v1.0.md`, `https://ipsoplatform.io/schema/edr/v1.json`) and drafts 0.1.0 and 0.2.0 stay published under their own names and are never edited.

Relative to version 1.0.0, version 1.1.0 makes two additive extensions:

1. Check engine `llm`, which counts toward `status: verified` and requires a relative `ref` naming its structured rule and fixture artifact (for example `.ipso/checks/firewall-egress.llm.json`). `llm-advisory` stays unchanged and does not count toward `verified`.
2. Optional check field `max_age_days` (`1` to `365`), naming the maximum age in days of a check verdict before consumers report the check as stale.

Every document that validates against draft 0.1.0, draft 0.2.0, or version 1.0.0 validates against version 1.1.0 with no edit and with the same meaning.

## 2. Document shape

A valid EDR file is UTF-8 Markdown. It starts with a YAML frontmatter block delimited by a line that is exactly `---`, then the YAML mapping, then a line that is exactly `---`. The body follows that second marker.

The frontmatter MUST be a single YAML mapping whose keys are only the fields in section 4. Unknown keys are invalid. The mapping MUST include every required field, and it MAY include `inferred` and `premises`.

The body MUST contain exactly one level-1 heading. After that heading, the body MUST contain these level-2 headings, in this order, each exactly once, and each with at least one paragraph or list:

1. `Context`
2. `Decision`
3. `Alternatives considered`
4. `Consequences`
5. `Verification`

A conventional path is `docs/decisions/NNNN-slug.md`, where `NNNN` is a four-digit number and `slug` is the frontmatter slug. A repository MAY store EDR files on another path. This version does not constrain the path.

A file with no frontmatter is not an EDR document. MADR and Nygard records without frontmatter become EDR documents only after an importer adds frontmatter. This version does not specify that importer.

## 3. Validation

Validate the parsed frontmatter mapping, not the Markdown bytes, against `schema/edr-v1.1.schema.json` (JSON Schema draft 2020-12). YAML dates MUST be treated as ISO `YYYY-MM-DD` strings before validation. YAML `null` stays null. Premise ids MUST be unique within `premises`. An `equals` value MUST be at most 1024 bytes when encoded as UTF-8 JSON with no insignificant whitespace, using the key order of the file. JSON Schema draft 2020-12 does not express either rule, so a conforming validator checks both after the schema.

A validator conforms to version 1.1.0 when every file in `examples/`, `examples/v0.2/`, and `examples/v1.1/` passes, and each file in `conformance/invalid/`, `conformance/invalid/v0.2/`, and `conformance/invalid/v1.1/` fails with the reason code in its sibling `.expect` file. `examples/v1.1/` holds positive records that version 1.0.0 rejects (`llm` engine or `max_age_days`). `conformance/invalid/v1.1/` holds negative fixtures for `llm` `ref` rules and `max_age_days` bounds. The `.expect` line is one reason code, matched in full: `frontmatter-start`, `frontmatter-close`, `frontmatter-mapping`, `additional-properties`, `status`, `owner`, `valid_to`, `scope`, `source-url`, `checks-empty`, `checks-deterministic`, `check-ref`, `check-max-age`, `heading-order`, `missing-heading`, `duplicate-heading`, `one-h1`, `section-empty`, `premises`, or `premise-id`. `check-ref` covers a missing or malformed `ref` on an engine that requires one (`snyk`, `wiz`, `sonarqube`, `dependabot`, or `llm`). `check-max-age` covers a `max_age_days` value that is not an integer from 1 to 365. `checks-deterministic` still covers a `verified` record whose checks are all `llm-advisory`. Each required level-2 heading appears once. A line that is exactly `---` is not a paragraph or a list, and after the frontmatter it stays in the body.

## 4. Frontmatter fields

### 4.1 `edr`

Required. Integer, always `1` in every 1.x version.

### 4.2 `id`

Required. A ULID: 26 characters from Crockford base32 (`0-9`, `A-H`, `J-K`, `M-N`, `P-T`, `V-Z`, excluding `I`, `L`, `O`, and `U`). The first character is `0` through `7`. The value is an opaque stable identifier. Do not derive it from the slug.

### 4.3 `slug`

Required. Lowercase kebab-case: words of `a-z` and `0-9`, separated by single hyphens. The slug is stable across status changes. A superseding decision uses a new slug.

### 4.4 `title`

Required. One non-empty line of human-readable text. The level-1 heading SHOULD match the title.

### 4.5 `status`

Required. One of:

| Status | Meaning |
| --- | --- |
| `proposed` | Waiting for approval |
| `accepted` | Approved. No authoritative check is enforcing it yet |
| `verified` | At least one authoritative check (`engine` other than `llm-advisory`) enforces it |
| `needs-review` | Stale, missing an owner, failing its check, or past `review_by` |
| `rejected` | Declined, and kept so the decline stays auditable |
| `superseded` | Replaced. `valid_to` MUST be a date |
| `deprecated` | Withdrawn with no successor required |

These tokens use hyphens. They are the file vocabulary. A product may show different words in a user interface. Those display words are not valid `status` values.

`verified` MUST include at least one check whose `engine` is not `llm-advisory`. Frontmatter validation checks structural declaration only; a schema-only consumer that does not inspect compiled check artifacts or runtime verdicts MUST NOT treat `status: verified` on an `engine: llm` record as proof that `allow`/`deny` fixtures or grounded input quotes were satisfied.

### 4.6 `owner`

Required. A team reference matching `team:` plus a lowercase kebab-case name, such as `team:platform`. The owner is a team, not a person.

### 4.7 `scope`

Required. A non-empty array of scope objects. Each object has at least one of `org`, `repo`, `paths`, and `env`. Unknown keys are invalid.

- `org` and `repo` are non-empty strings. `repo` MAY be a glob, such as `acme/infra-*`.
- `paths` and `env` are non-empty arrays of non-empty strings.

How a selector matches a repository, path, or environment is product behavior. This version constrains the shape only. One scope object MAY set only `org`.

### 4.8 `valid_from`, `valid_to`, `review_by`

Required. `valid_from` and `review_by` are dates (`YYYY-MM-DD`). `valid_to` is a date or `null`. `null` means the decision has no end date. When `status` is `superseded`, `valid_to` MUST be a date.

`valid_from` and `valid_to` are valid time: when the decision binds the world. They are not the time the file was written.

### 4.9 `supersedes`

Required. An array of slugs, possibly empty. Each slug is a decision this record replaces. The field points backward, from the successor to the older slug. A record whose own `status` is `superseded` MAY leave `supersedes` empty. The successor lists the old slug.

### 4.10 `criticality`

Required. One of `blocking`, `warning`, or `advisory`.

### 4.11 `checks`

Required. An array of check objects, possibly empty. An empty array means nothing verifies the decision yet.

Each check object requires `engine` and MAY set `target`, `ref`, `job`, `mode`, and `max_age_days`. Unknown keys are invalid. An implementation ignores optional keys that do not apply to that engine.

`engine` is one of: `opa`, `semgrep`, `archunit`, `depcruise`, `checkov`, `kyverno`, `eslint`, `importlinter`, `ci-binding`, `snyk`, `wiz`, `sonarqube`, `dependabot`, `llm`, `llm-advisory`.

`mode` is one of `block`, `warn`, or `advise`. `llm-advisory` is not an authoritative check and never satisfies `status: verified`.

`target` and `ref` name the subject and the rule artifact. `job` names an existing CI job that supplies evidence (`ci-binding`).

- For `snyk`, `wiz`, `sonarqube`, and `dependabot`, `ref` MUST be present (1 to 128 characters, no ASCII control character, no leading or trailing space).
- For `llm`, `ref` MUST be present (1 to 255 characters, repository-relative forward-slash path without leading or trailing `/`, `:` scheme or drive separators, `\` backslashes, empty `//` segments, `..` path segments, ASCII control characters, or spaces, such as `.ipso/checks/firewall-egress.llm.json`). Compilers that support `mode: block` on `llm` MUST require approved `allow` and `deny` fixtures in the referenced artifact and evaluators MUST require verbatim input quotes at evaluation time; schema-only consumers MUST NOT treat schema validity alone as enforcement proof.
- `max_age_days` is an optional integer from `1` to `365` inclusive. When set, consumers treat a check whose latest verdict is older than `max_age_days` days as stale. When omitted, a consumer MAY apply its own default window.

### 4.12 `waivers`

Required. An array of waiver objects, possibly empty. A waiver object requires `reason`, `approver`, `valid_from`, and `valid_to`. All four are non-empty strings except the two dates, which are `YYYY-MM-DD`. `valid_to` on a waiver is a date, not null. `approver` MAY be a person or a team reference. Unknown keys are invalid.

### 4.13 `sources`

Required. A non-empty array of source objects. Each object requires `type` (a non-empty string, such as `pr`, `spec`, or `adr`) and `url` (an `https` URI). Unknown keys are invalid.

### 4.14 `tags`

Required. An array of kebab-case strings, possibly empty.

### 4.15 `inferred`

Optional boolean. Omit it, or set `false`, when a person wrote every value. An importer sets `true` when it invented a value that was not in the source document. Absence means `false`.

### 4.16 `premises`

Optional. Omit it, or set an array of at most 20 premise objects. An empty array and an omitted key both mean the decision records no premises. An array of 21 or more is invalid.

Each premise object requires `id`, `statement`, `every`, and `probe`. Unknown keys are invalid. `id` uses the same lowercase kebab-case as `slug` and MUST be unique within the array. `statement` is a string of 1 to 500 characters, counted as JSON Schema string length (Unicode code points). `every` is an integer from 1 to 365 inclusive, the interval in days at which a consumer rechecks the premise.

`probe` requires `url` and `pointer`, and exactly one of `equals` or `semver`. Unknown keys are invalid, including `script`, `header`, and `authorization`. A probe with both comparisons, or with neither, is invalid.

`url` matches `https://` followed by one or more non-whitespace characters, the same pattern as a source URL. A consumer that fetches the URL MUST take it verbatim and MUST refuse userinfo, a fragment, a host that is not a DNS name, a port other than 443, a redirect, and any resolved address that is not a public unicast address. It MUST send no cookies and no `Authorization` header, with a 10-second timeout and a 1 MiB body limit.

`pointer` is a JSON Pointer (RFC 6901). `equals` is any JSON value whose compact UTF-8 JSON encoding is at most 1024 bytes. `semver` is an operator (`=`, `>`, `>=`, `<`, or `<=`) immediately followed by `MAJOR.MINOR.PATCH` and an optional pre-release, with no space and no build metadata.

Changing a premise changes the decision version. A premise is not a check.

## 5. Minimal 1.1 document

```yaml
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
```

The full record is `examples/v1.1/llm-verified-check.md`. A deterministic check that sets `max_age_days` is `examples/v1.1/check-max-age-days.md`.

## 6. Versioning

This document follows SemVer, `MAJOR.MINOR.PATCH`, and the `edr` key carries the major generation.

- A patch changes prose only. It does not change which documents are valid or the reason codes of the negative corpus. A patch edits this text in place and leaves the schema file as it is.
- A minor only adds. It may add an optional key, add an enum value, or widen a limit. Every document that is valid at the previous version stays valid with the same meaning. A minor publishes a new text `spec/edr-v1.N.md`, a new schema `schema/edr-v1.N.schema.json`, and a new `$id` `https://ipsoplatform.io/schema/edr/v1.N.json`. It keeps `edr: 1`.
- A major is any other change. It sets `edr: 2`, publishes new files and a new `$id`, and ships a migration guide.

From 1.0.0 on, a published schema file never changes, and its `$id` names that file alone. `https://ipsoplatform.io/schema/edr/v1.json` names the 1.0.0 schema, and `https://ipsoplatform.io/schema/edr/v1.1.json` names the 1.1.0 schema. Each release is recorded in `CHANGELOG.md`, and `MIGRATING.md` covers the move from an earlier version.
