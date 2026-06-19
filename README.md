# Medisure Canada Lead Data

A versioned JSON standard for Canadian healthcare and legal lead generation.

## Current repository status

The canonical data contract has been merged into `main` and includes:

- JSON Schema for lead collections
- JSON Schema for source provenance and usage policies
- Canada province/territory reference using postal, ISO 3166-2 and Statistics Canada SGC codes
- Healthcare, legal, Medisure-product, workflow and contact-verification taxonomies
- Data-model and deduplication documentation
- An initialized legal-lead collection covering all 13 provinces and territories

The supplied Excel workbook has also been converted into a separately packaged, validated bulk import containing:

- **103 curated healthcare accounts**
- **1,246 deduplicated healthcare facility candidates** from 1,429 ODHF source rows
- **208 registered source records**
- Coverage across all **10 provinces and 3 territories**

The bulk package is partitioned by province/territory so it can be reviewed and committed without creating one unmanageably large JSON file.

## Repository layout

```text
schemas/
  lead-collection.schema.json
  source-registry.schema.json
reference/
  canada-jurisdictions.json
  taxonomy.json
docs/
  DATA_MODEL.md
data/
  healthcare/
    curated-accounts.json
    odhf-facilities-<PT>.json
  legal/
    legal-leads.json
sources/
  source-registry.json
scripts/
  validate_data.py
```

## Canonical storage rule

Store one JSON record per targetable entity:

- Hospital system, health authority, clinic network or law firm: `organization`
- Physical hospital, clinic, long-term-care site or law office: `facility`
- Lawyer, notary, paralegal or named decision-maker: `professional`

Use a stable parent ID to connect facilities and professionals to their organization. Preserve source-specific wording while also storing normalized classifications.

## Canada geography

Canada does not use states. Local records use this hierarchy:

1. Geographical region
2. Province or territory
3. Census division
4. Census subdivision

Province/territory is required for local records. Census division and subdivision may remain `null` until a reliable SGC match is available.

## Data quality and compliance

- Deduplicate organizations by normalized name, jurisdiction and website domain.
- Deduplicate facilities by normalized name, postal code and jurisdiction.
- Keep exact source URLs and source review dates.
- Do not store guessed emails as verified contacts.
- Treat law-society directories as professional-verification sources unless their terms expressly allow broader use.
- Complete CASL review, permission-basis and suppression fields before campaign enrollment.

This repository is a lead-research system, not proof of consent and not legal advice.
