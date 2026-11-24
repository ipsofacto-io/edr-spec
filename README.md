# edr-spec

[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/ipsofacto-io/edr-spec/badge)](https://scorecard.dev/viewer/?uri=github.com/ipsofacto-io/edr-spec)

Open specification and JSON Schema for the Engineering Decision Record (EDR) format.

> Status: 1.1.0, stable. Normative text in [spec/edr-v1.1.md](spec/edr-v1.1.md). Schema `$id` is `https://ipsoplatform.io/schema/edr/v1.1.json`. Version 1.0.0 ([spec/edr-v1.0.md](spec/edr-v1.0.md), `https://ipsoplatform.io/schema/edr/v1.json`), draft RFC 0.2.0 ([spec/edr-v0.2.md](spec/edr-v0.2.md), `https://ipsoplatform.io/schema/edr/v0.2.json`), and draft RFC 0.1.0 ([spec/edr-v0.1.md](spec/edr-v0.1.md), `https://ipsoplatform.io/schema/edr/v0.1.json`) remain published and immutable.

The EDR format is the open record for a binding engineering decision: owner, scope, validity period, the check that verifies it, and the premises it rests on. This repository is the standards process for that format. Prose and schema use different licenses, so the text can be republished and the schema can be embedded in Apache-2.0 implementations.

## What lives here

- [spec/edr-v1.1.md](spec/edr-v1.1.md): 1.1.0, stable. Adds check engine `llm` (with required relative `ref`) and optional `check.max_age_days` (`1..365`). Accepts every 0.1.0, 0.2.0, and 1.0.0 document unchanged.
- [spec/edr-v1.0.md](spec/edr-v1.0.md): 1.0.0, stable. It freezes draft 0.2.0, so a document is valid at 1.0.0 exactly when it is valid at 0.2.0. Optional `premises`. A file that validates against 0.1.0 or 0.2.0 also validates against 1.0.0. A MADR or Nygard file without frontmatter is not an EDR document until an importer adds frontmatter. Section 6 states the versioning rules.
- [spec/edr-v0.2.md](spec/edr-v0.2.md): Draft RFC 0.2.0, not edited from 1.0.0 on. Superseded by 1.0.0.
- [spec/edr-v0.1.md](spec/edr-v0.1.md): Draft RFC 0.1.0, not edited from 1.0.0 on. It has no `premises` key.
- [schema/edr-v1.1.schema.json](schema/edr-v1.1.schema.json), [schema/edr-v1.0.schema.json](schema/edr-v1.0.schema.json), [schema/edr-v0.2.schema.json](schema/edr-v0.2.schema.json), and [schema/edr-v0.1.schema.json](schema/edr-v0.1.schema.json): JSON Schema for the frontmatter of each version. From 1.0.0 on, a published schema file never changes. The 1.0 schema differs from the 0.2 schema only in `$id` and `title`.
- [CHANGELOG.md](CHANGELOG.md) lists the notable changes since 0.1.0, and [MIGRATING.md](MIGRATING.md) covers the move to 1.0 and 1.1.
- [examples/](examples/): 21 positive fixtures, valid for every version. [examples/v0.2/](examples/v0.2/): records valid for 0.2.0, 1.0.0, and 1.1.0. [examples/v1.1/](examples/v1.1/): records valid for 1.1.0 (`llm` engine and `max_age_days`). [conformance/invalid/](conformance/invalid/): negative fixtures for every version. [conformance/invalid/v0.2/](conformance/invalid/v0.2/): premises negatives. [conformance/invalid/v1.1/](conformance/invalid/v1.1/): 1.1 negatives. Each `.expect` file is one stable reason code from section 3 of the text, matched exactly. `tools/validate_examples.py` checks all four schemas in CI.

An external implementer runs the suite with:

```bash
python -m pip install --disable-pip-version-check PyYAML==6.0.3 jsonschema==4.26.0
python3 tools/validate_examples.py
```

## Versioning

The format follows SemVer. A patch changes prose only. A minor only adds and publishes new files with a new `$id`, such as `https://ipsoplatform.io/schema/edr/v1.1.json`. A major sets `edr: 2`. `https://ipsoplatform.io/schema/edr/v1.json` names the 1.0.0 schema and is not an alias for the latest 1.x schema. Section 6 of [spec/edr-v1.0.md](spec/edr-v1.0.md) has the full rules.

## License

| Material | License | Text |
| --- | --- | --- |
| Specification prose, including this README | CC-BY-4.0 | [LICENSE-CC-BY](LICENSE-CC-BY) |
| JSON Schema and the conformance corpus | Apache-2.0 | [LICENSE-APACHE](LICENSE-APACHE) |

[LICENSE](LICENSE) states the split. Copyright 2026 Luca Bertelli.

## Contributing

Follow the contributor terms in [ipsofacto-io/.github](https://github.com/ipsofacto-io/.github/blob/main/CONTRIBUTING.md). Commits need a `Signed-off-by` trailer (`git commit -s`).
