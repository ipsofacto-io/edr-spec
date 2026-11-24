# Migrating to EDR 1.0

EDR 1.0.0 freezes draft 0.2.0. It adds, renames, and removes nothing that draft 0.2.0 defines. The two sections below cover a person who writes EDR files and a person who implements a validator or another consumer.

## Writing EDR files

No edit is needed. Every file that is valid at draft 0.1.0 or draft 0.2.0 is valid at 1.0.0, with `edr: 1` unchanged.

A 1.0.0 file reads cleanly in a draft 0.1.0 validator only if it omits `premises` and does not use the `snyk`, `wiz`, `sonarqube`, or `dependabot` engines. Draft 0.1.0 rejects a document that sets `premises` as `additional-properties`, and it rejects a document that uses one of those engines because the engine is not one of its own.

## Implementing a validator or a consumer

### From draft 0.2.0

1. Load `schema/edr-v1.0.schema.json`. It is the draft 0.2.0 schema with a new `$id`, `https://ipsoplatform.io/schema/edr/v1.json`, and a new `title`.
2. If you resolve the schema by `$id`, register the new `$id`. A validator that keeps the draft 0.2.0 file of 2026-11-19 or later accepts the same documents, and it names a superseded schema. A copy of that file from before 2026-11-19 lacks the four security tool engines, so replace it.
3. Change nothing else. The checks after the schema, the reason codes, and the corpus directories are the same.
4. Run the conformance suite as section 3 of `spec/edr-v1.0.md` describes. The corpus paths did not change.

### From draft 0.1.0

Do the steps above, and add what draft 0.2.0 introduced:

1. Accept the optional `premises` array, as section 4.16 describes.
2. After the schema, check that premise ids are unique within a document, and that an `equals` value is at most 1024 bytes as compact UTF-8 JSON in the key order of the file. JSON Schema does not express either rule.
3. Report the reason codes `premises` and `premise-id` for the fixtures in `conformance/invalid/v0.2/`.
4. Accept the engines `snyk`, `wiz`, `sonarqube`, and `dependabot`. A check for one of them requires `ref`, the finding id, at most 128 characters, with no ASCII control character and no leading or trailing space.
5. If you fetch a premise URL, follow the consumer obligations in section 4.16. The schema does not check them.

## Moving to 1.1.0

1. Existing 0.1.0, 0.2.0, and 1.0.0 files need no edit at 1.1.0.
2. Validators adopting 1.1.0 load `schema/edr-v1.1.schema.json` (`https://ipsoplatform.io/schema/edr/v1.1.json`), accept `engine: llm` (with required relative `ref`) and optional `check.max_age_days` (`1..365`), and report `check-ref` and `check-max-age` on `conformance/invalid/v1.1/`.

## After 1.0.0

A minor version only adds, and it publishes a new schema file with a new `$id`, such as `https://ipsoplatform.io/schema/edr/v1.1.json`. `https://ipsoplatform.io/schema/edr/v1.json` stays the 1.0.0 schema. State which version your validator accepts, because a validator for 1.0.0 may reject a later minor's addition, such as a new key or a new enum value. A major version sets `edr: 2` and ships its own migration guide. Section 6 of the text has the rules.
