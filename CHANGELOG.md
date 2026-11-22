# Changelog

Notable changes to the specification text, the JSON Schema, the conformance corpus, and the validator script. Changes to CI workflows, dependency pins, and repository metadata are not listed. Versions follow section 6 of the specification. Newest first.

## 1.0.0 - 2026-11-22

Stable. 1.0.0 freezes draft 0.2.0. A document is valid at 1.0.0 exactly when it is valid at draft 0.2.0, so every 0.1.0 and 0.2.0 file is a 1.0.0 file with no edit.

### Added

- `spec/edr-v1.0.md`, the normative text, with status Stable.
- `schema/edr-v1.0.schema.json`, with `$id` `https://ipsoplatform.io/schema/edr/v1.json`. Earlier drafts reserved that URI for this release.
- Section 6 of the text states the versioning rules: a patch changes prose only, a minor only adds and publishes new files with a new `$id`, a major sets `edr: 2`, and from 1.0.0 on a published schema file never changes.
- `CHANGELOG.md` and `MIGRATING.md`.
- `tools/validate_examples.py` runs the whole corpus against the 1.0 schema and checks that the 1.0 schema differs from the draft 0.2 schema only in `$id` and `title`.

### Changed

- Nothing in the accepted set. No key, enum value, limit, or reason code is added, renamed, or removed.
- Drafts 0.1.0 and 0.2.0, their schemas, and the corpus paths stay as they are from 1.0.0 on. Before 1.0.0 the drafts were corrected in place, as the entries below record. A copy of the draft 0.2.0 schema cached before 2026-11-19 lacks the four security tool engines.

### Feedback behind the freeze

- The public RFC has been open since 2026-10-20 with issues and discussions enabled. It received no issue, no discussion, and no comment.
- Dogfood: the 161 decision files that the project keeps, which include the public examples repository, were validated against draft 0.2.0. 158 pass. The other three fail for file defects and not format defects: two superseded stubs without the five required sections, and one `id` of 25 characters. No file needed a format change.
- The format changes that the project's own use asked for are already in 0.2.0: premises and the security tool engines.

## 0.2.0 - 2026-10-31

Draft RFC. Accepts every 0.1.0 file. Amended on 2026-11-19.

### Added

- Optional `premises`: at most 20 objects per decision. Each has `id`, `statement` (1 to 500 characters), `every` (1 to 365 days), and a `probe` with `url`, `pointer`, and exactly one of `equals` or `semver`. Unknown keys, including `script`, `header`, and `authorization`, are invalid.
- Two checks after the schema, because JSON Schema does not express them: premise ids are unique within a decision, and an `equals` value is at most 1024 bytes as compact UTF-8 JSON.
- Reason codes `premises` and `premise-id`.
- `examples/v0.2/` with positive records that draft 0.1.0 rejects, and `conformance/invalid/v0.2/` with 11 negative fixtures.
- 2026-11-19: the engines `snyk`, `wiz`, `sonarqube`, and `dependabot`, added in place under the same `$id`. A check for one of them requires `ref`, which is the finding id, at most 128 characters. `examples/v0.2/snyk-open-finding.md` is the example.

## 0.1.0 - 2026-10-20

First draft RFC. Corrections were made in place afterward. They changed no schema rule. One of them, on 2026-10-21, made the body rule explicit that each required level-2 heading appears exactly once, and it added the `duplicate-heading` fixture.

### Added

- `spec/edr-v0.1.md`, `schema/edr-v0.1.schema.json`, and the positive corpus in `examples/`.
- 2026-10-21: the negative corpus in `conformance/invalid/`. Each fixture has a `.expect` file with one stable reason code, matched in full. `tools/validate_examples.py` classifies a failure by schema path. A `---` line in the body after the frontmatter stays in the body (`examples/21-body-horizontal-rule.md`). The reason codes `frontmatter-close` and `frontmatter-mapping` were added to the text.

### Changed

- 2026-10-24 to 2026-10-27: the module-boundaries, ArchUnit, and ESLint examples name a JSON rule file.
- 2026-10-20 and 2026-10-27: the draft schema `$id` host was changed twice in place and ends at `ipsoplatform.io`. A copy of the 0.1.0 schema taken before 2026-10-27 has a different `$id` host and the same rules. The text also reads EDR as Engineering Decision Record.
