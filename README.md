# CluePoints Interview - SDTM Data Transformation - Databricks

This demo is for Philip Rolph interview with Cluepoints - a very simple Data Lakehouse in Databricks.

This is the first time working with clinical trial data and I found it very interesting. I researched SDTM domains and can appreciate the similarity with standardised data domains I've previously worked with such as the FHIR model within healthcare.

With no prior knowledge of the business domain, I focused on displaying some basic techniques around building a Lakehouse architecture in Databricks. Hopefully this is useful.

## Domains Covered

- **DM (Demographics)**
- **AE (Adverse Events)**
- **EX (Exposure)**

## Usage

These notebooks will run in any Databricks workspace - the source data is pulled from a public GitHub repo at runtime.

The repo will be deleted after the demo.

Run notebooks in order - number 1 to number 7 to load the bronze, silver and gold layers - observe they run to success.

Mapping tables and final datasets are included in notebook number 8.

## Idempotency

The idempotency model merges data to bronze and performs a full overwrite on silver and gold to preserve provenance in the bronze layer.

However, to design an idempotency model for production more context is needed to apply guarantees at different levels to be more efficient.

Questions to assist further development of idempotency model:

1. Can we expect new 'versions' of an incoming file that may have additional rows or amended rows?
2. Can we apply a checksum to the incoming file so we can idempotently avoid reprocessing identical files unnecessarily? (checksum is much less costly than performing spark merge on same data)
3. How large is the incoming file in production - millions of rows, many GB in size?
4. Are we always processing one trial to one table i.e. not multi-tenanted tables?
5. Do we need to support schema evolution automatically? How to manage this breaking change?

Depending on the answers to the above questions I would change the way by which we process incoming files, potentialy seperating inserts and updates or updating on row checksums rather than forcing a merge.


## Optimisation

1. Given the small file size there is currently no performance optimisation required but for production volumes consider:
- Optimize - file compaction after multiple incremental loads
- Z-Order - on SUBJID and date columns for fast filtering
- Bloom Filters - on high cardinality columns like SUBJID for point lookups

## Production Readiness

Further enhancements required to productionise this code:

- Logging within Databricks
- Enhanced logging or integration with observability stack
- Error handling
- Data quality checks including NULLs and lookups on key reference data
- More sophisticated runtime metadata and provenance tracking
- Validation on row counts
- Externalised config

## THANKS FOR INVITING ME TO PARTICIPATE