# Data model

Every data file contains a collection envelope with `schema_version`, `dataset`, and `records`.

## Required record sections

- `id`: stable deterministic identifier.
- `sector`: `healthcare` or `legal`.
- `record_type`: `organization`, `facility`, or `professional`.
- `entity`: normalized identity plus original segment.
- `geography`: Canada, province/territory, ISO and SGC codes, locality/address when known.
- `source_ids`: references into `sources/source-registry.json`.
- `quality.dedupe_key`: SHA-256 key used to prevent duplicates.
- `audit`: creation/update timestamps and original workbook rows.

## Optional enrichment sections

- `contacts`: public organization-level channels.
- `target_roles`: roles to research, not named people.
- `enrichment`: repeatable search queries and procurement route.
- `medisure`: product fit, outreach angle and score.
- `workflow`: owner and pipeline status.
- `compliance`: CASL review, permission and suppression fields.

## Deduplication rules

- Organization: normalized name + province/territory + website domain.
- Facility: normalized name + postal code + province/territory.
- Law firm: normalized name + domain + province/territory.
- Legal professional: regulator membership number where available; otherwise normalized name + firm + province/territory.

Do not store guessed email patterns as verified emails. Preserve exact source URLs and review the source's licence/terms before automated collection or outreach.
