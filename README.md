# Medisure Canada Lead Data

A versioned JSON repository for Canadian healthcare and legal lead generation.

## Current migration

- **103 curated healthcare accounts** were imported from `BD_Target_Accounts` and merged with `Contact_Details`.
- **1,429 ODHF source rows** were normalized and deduplicated into **1,246 facility candidates**.
- Healthcare facility coverage includes all **10 provinces and 3 territories**.
- The legal collection is initialized but intentionally contains no professional leads until a compliant source and usage review is completed.
- The source registry records the URL, publisher, jurisdiction, licence/terms status and permitted use for every source used by a record.

## Repository layout

```text
schemas/
  lead-collection.schema.json      Standard record and collection schema
  source-registry.schema.json      Source provenance and usage-policy schema
reference/
  canada-jurisdictions.json        Province/territory, ISO and SGC codes
  taxonomy.json                    Sectors, entity types, statuses and product codes
sources/
  source-registry.json             Source URLs, terms/usage notes and review dates
data/
  healthcare/
    curated-accounts.json          Sales-ready account universe; still requires contact verification
    odhf-facilities-<PT>.json      Facility candidates partitioned by province/territory
  legal/
    legal-leads.json               Law-firm and legal-professional collection
docs/
  DATA_MODEL.md                    Field definitions, examples and ingestion rules
scripts/
  validate_data.py                 JSON Schema, ID, geography and source-reference checks
```

## Canonical storage rule

Store **one JSON record per targetable entity**:

- A hospital system, health authority, clinic network or law firm is an `organization`.
- A physical hospital, clinic, long-term-care site or office is a `facility`.
- A lawyer, notary, paralegal or named decision-maker is a `professional`.
- Use `entity.parent_lead_id` to link a facility or professional to its organization.
- Keep every source in `provenance`, and register the source in `sources/source-registry.json`.
- Never overwrite source-specific wording. Put normalized values in standard fields and preserve original wording in `segment_raw`, `classification`, or source notes.

## Canada geography

Canada does not use states. Records use the Statistics Canada hierarchy:

1. Geographical region
2. Province or territory
3. Census division
4. Census subdivision

Province/territory is mandatory for local records. Census division/subdivision fields can remain `null` until a reliable SGC match is available.

## Lead workflow

1. Add or review a source in `sources/source-registry.json`.
2. Import records into the standard schema.
3. Deduplicate using `data_quality.dedupe_key`.
4. Validate the organization, website, active status and target fit.
5. Enrich named contacts only through sources and tools permitted for that use.
6. Verify business emails and record the source and verification date.
7. Complete the CASL review before campaign enrollment.
8. Run the validator and commit the changes.

## Legal-data rule

Law-society directories are primarily regulatory or public-interest lookup tools. Some explicitly prohibit commercial, marketing or solicitation use. They must not be bulk scraped or treated as marketing lists. Prefer law-firm websites, public firm contact pages and approved B2B providers, then use the regulator only to verify professional status where permitted.

## Validate

```bash
python -m pip install -r requirements.txt
python scripts/validate_data.py
```

The GitHub Actions workflow runs the same validation on every push and pull request.

## Source and outreach safeguards

Every outbound contact should have:

- an identifiable public business source;
- a source review date;
- a recorded consent or other applicable CASL basis;
- Medisure identification and contact details in the message;
- a working unsubscribe mechanism;
- suppression handling for opt-outs, invalid addresses and do-not-contact records.

This repository is a lead-research system, not proof of consent and not legal advice.
