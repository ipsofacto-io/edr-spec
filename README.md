# edr-spec

[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/ipsofacto-io/edr-spec/badge)](https://scorecard.dev/viewer/?uri=github.com/ipsofacto-io/edr-spec)

Open specification, JSON Schema, and conformance suite for the **Engineering Decision Record (EDR)** format.

> Latest stable version: **1.1.0** ([spec](spec/edr-v1.1.md), [schema](schema/edr-v1.1.schema.json), `$id`: `https://ipsoplatform.io/schema/edr/v1.1.json`). All previously published versions remain immutable.

## What is an EDR?

An Engineering Decision Record is a Markdown file with structured YAML frontmatter that turns an architectural or operational decision into a binding, verifiable record. Each record captures:

- **Ownership and scope:** Who owns the decision and which organizations, repos, paths, or environments it governs.
- **Validity window:** When the decision takes effect, when it expires or is reviewed, and what it supersedes.
- **Verification checks:** Which automated checks enforce the decision and whether violations block, warn, or advise.
- **Premises:** The underlying assumptions that can be re-checked over time.

Existing MADR or Nygard ADRs without YAML frontmatter are not EDR documents until an importer adds the frontmatter.

## Published versions

| Version | Status | Specification | JSON Schema | Schema `$id` | Notes |
| --- | --- | --- | --- | --- | --- |
| 1.1.0 | Stable (latest) | [spec/edr-v1.1.md](spec/edr-v1.1.md) | [schema/edr-v1.1.schema.json](schema/edr-v1.1.schema.json) | `https://ipsoplatform.io/schema/edr/v1.1.json` | Adds `llm` check engine (with required relative `ref`) and optional `check.max_age_days` (`1..365`); backwards-compatible with 0.1.0, 0.2.0, and 1.0.0 |
| 1.0.0 | Stable | [spec/edr-v1.0.md](spec/edr-v1.0.md) | [schema/edr-v1.0.schema.json](schema/edr-v1.0.schema.json) | `https://ipsoplatform.io/schema/edr/v1.json` | Freezes draft 0.2.0 (identical validation rules; differs only in `$id` and `title`); adds optional `premises` |
| 0.2.0 | Draft RFC (superseded) | [spec/edr-v0.2.md](spec/edr-v0.2.md) | [schema/edr-v0.2.schema.json](schema/edr-v0.2.schema.json) | `https://ipsoplatform.io/schema/edr/v0.2.json` | Frozen as of 1.0.0; superseded by 1.0.0 |
| 0.1.0 | Draft RFC (superseded) | [spec/edr-v0.1.md](spec/edr-v0.1.md) | [schema/edr-v0.1.schema.json](schema/edr-v0.1.schema.json) | `https://ipsoplatform.io/schema/edr/v0.1.json` | Frozen as of 1.0.0; predates `premises` |

See [CHANGELOG.md](CHANGELOG.md) for notable changes and [MIGRATING.md](MIGRATING.md) for upgrading to 1.0 and 1.1.

## Repository layout

| Path | Contents |
| --- | --- |
| [`spec/`](spec/) | Normative specification documents for each published version |
| [`schema/`](schema/) | Immutable JSON Schemas for EDR YAML frontmatter |
| [`examples/`](examples/) | Positive fixtures valid across all versions (21 base fixtures), plus version-specific fixtures under [`examples/v0.2/`](examples/v0.2/) and [`examples/v1.1/`](examples/v1.1/) |
| [`conformance/invalid/`](conformance/invalid/) | Negative fixtures for every version, plus [`conformance/invalid/v0.2/`](conformance/invalid/v0.2/) and [`conformance/invalid/v1.1/`](conformance/invalid/v1.1/); each `.expect` file matches one stable reason code from section 3 of the spec |
| [`tools/validate_examples.py`](tools/validate_examples.py) | Reference conformance runner that validates all fixtures against all four schemas in CI |

## Run the conformance suite

Implementers can verify the full fixture corpus locally:

```bash
python -m pip install --disable-pip-version-check PyYAML==6.0.3 jsonschema==4.26.0
python3 tools/validate_examples.py
```

## Versioning rules

The EDR format follows Semantic Versioning (full rules in Section 6 of [spec/edr-v1.0.md](spec/edr-v1.0.md)):

- **Patch:** Prose clarifications only; schemas never change.
- **Minor:** Additive changes published as new files with a new `$id` (for example `https://ipsoplatform.io/schema/edr/v1.1.json`). Existing schema files are immutable from 1.0.0 onward.
- **Major:** Breaking changes bump the frontmatter version to `edr: 2`.
- **No floating aliases:** `https://ipsoplatform.io/schema/edr/v1.json` permanently identifies 1.0.0, not "latest 1.x".

## License

Prose and schemas are dual-licensed so the specification can be freely republished while the schemas and tests can be embedded in Apache-2.0 implementations.

| Material | License | License text |
| --- | --- | --- |
| Specification prose (including this README) | CC-BY-4.0 | [LICENSE-CC-BY](LICENSE-CC-BY) |
| JSON Schemas and conformance corpus | Apache-2.0 | [LICENSE-APACHE](LICENSE-APACHE) |

See [LICENSE](LICENSE) for the full split. Copyright 2026 Luca Bertelli.

## Contributing

Follow the contributor guidelines in [ipsofacto-io/.github](https://github.com/ipsofacto-io/.github/blob/main/CONTRIBUTING.md). All commits require a `Signed-off-by` trailer (`git commit -s`).
