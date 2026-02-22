# Full Load Concept

**Purpose:**  
Full Load is used to ingest the entire table from the source system into the Bronze layer. It is typically used for onboarding new tables, rebuilding datasets, or during major corrections.

## Key Features
- Reloads **entire table** regardless of previous CDC values.  
- Parameterized via pipeline parameter:  
  - `load_type = full`  
- Does **not** update `cdc.json`.  
- Idempotent — safe to rerun without affecting incremental tracking.  
- Supports multiple tables and schemas dynamically.  

## Full Load Flow
1. Lookup CDC JSON (optional) – output ignored.  
2. Set current UTC variable (optional).  
3. Copy Activity:  
   - SQL Query dynamically generates:  
     ```sql
     SELECT * FROM <schema>.<table>
     WHERE 1=1
     ```
   - Sink: ADLS2 Bronze layer (Parquet)  
   - Output folder: `bronze/data_parquet/<table>_<current_utc>/`  
4. Skip updating `cdc.json`.  
5. Optional: Trigger WebActivity or alert for monitoring.  

## When to Use Full Load
- Initial table onboarding.  
- Rebuilding Bronze layer due to corruption or schema changes.  
- Periodic table reloads in rare scenarios.  

## Notes
- Parquet format ensures efficient storage and future transformations.  
- Fully parameterized for enterprise-grade reusability.  
- Safe for production — incremental pipeline logic remains unaffected.
