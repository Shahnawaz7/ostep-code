Monday review: SODM layout and table setup

1. What's done

• STG tables (11) moved to d365_tst.external_staging (bronze). Excel, Salesforce and Oracle feeds will land here.
• stg_sol_sales_order stays in sodm_silver, because it directly feeds fct_sol_sales_order.
• 25 DIM/FCT/AGG tables moved from sodm_gold to sodm_silver, with owner Administrators.
• sodm_gold is empty, ready for the Tableau views.
• ALTER script saved, not run: "SODM Silver ALTER constraints and clustering".

2. The script: 281 statements across 26 tables

• 239 NOT NULL, copied from Redshift. Enforced: bad rows get rejected.
• 16 PKs with RELY, on each DIM table's first column. Informational: they help the optimizer and BI tools, but uniqueness comes from the MERGE.
• 26 CLUSTER BY, from the Redshift sort keys, at most 4 columns. This replaces indexing by skipping files a query doesn't need.
• Order within each table: NOT NULL, then CLUSTER BY, then PK.
• dim_sol_price_list PK is commented out, because product_attribute_key may not be unique.

3. Kevin's doc versus the current plan

• Agrees on: one catalog, silver and gold schemas, separate sandboxes, gold as views, informational PKs.
• Difference 1: Kevin's doc puts the 3 AGG tables in gold; currently they're in silver.
• Difference 2: Kevin's doc says to verify clustering with real workloads; currently it copies the Redshift sort keys.
• Note: dim_contact is restricted personal data.

4. Decisions needed from Isaac

1. AGG tables: silver or gold?
2. Clustering approach (my proposal is in the recommendations below).
3. fct_sol_sales_order: add cap_program_year as the first cluster column?
4. Real key for dim_sol_price_list?
5. Keep the external_staging name?
6. Snapshot history: one-time copy of 150M rows, or rebuild? My 2025 week 10 check: stored $62.06M / 136,814 units versus rebuilt $61.25M / 317,627.
7. Approval to run the script.

5. Needed from Isaac

• Seed scripts for dim_edw_control, dim_edw_process, dim_date, dim_week and dim_data_source.
• List of Tableau data sources and the tables each uses, to design the gold views.

6. Next steps
Seed loads → Excel ingestion → Salesforce/Oracle STG loads → convert sp_fct_sol_sales_order to a MERGE → gold views.

My recommendation, if they ask for your view

Combine Isaac's and Kevin's positions; they're closer than they look. Isaac wants a curated star schema that's fast in Tableau, and Kevin wants clean layers and evidence-based tuning.

1. Layers

• Bronze: the source catalogs plus external_staging. Raw data, no business logic.
• Silver: the star schema (conformed DIM and FCT tables), plus STG intermediates and control tables. This is the single source of truth.
• Gold: AGG tables plus views or materialized views for Tableau. I'd move the AGG tables to gold now, because they're report-shaped already.
• Tableau and BI service principals get read access to gold only.

2. Shared dimensions

• Keep them in sodm_silver for now.
• When the second data mart starts (PT/scores), move the shared dimensions (customer, product, date, organization) to cap_edw_tst.common_silver, so they aren't duplicated. Decide this before the second mart, not after.

3. Clustering, phased

• Now: apply NOT NULL and the PKs. Cluster only the large tables (fct_sol_sales_order, fct_or_snapshot_detail, stg_sol_sales_order, fct_sol_invoice_history) using the sort keys, with cap_program_year first on the fact table. Skip clustering on the small dimensions.
• After data is loaded and Tableau runs: check query history, try CLUSTER BY AUTO on the medium tables, and adjust. Cluster keys can be changed at any time with no downtime.
• This follows Isaac's direction and Kevin's "verify, don't copy."

4. Gold views

• Start with one materialized view for the main sales-order Tableau source, and measure Tableau's speed against today.
• If it's clearly faster, repeat for the other sources. Keep dimension views in gold too, so the current Tableau semantic layer still works during the transition.

5. Snapshot history

• Do a one-time copy of the history. Rebuilding changes past numbers, as the check above showed, and reports would no longer match what users saw before.

6. Quality and governance

• Add duplicate checks on PK grain after each MERGE.
• Reconcile row counts and totals against Redshift for each table during parallel run.
• Isaac owns promotion of anything into silver or gold.
• Move to Asset Bundles for deployment (dev → tst → prod) instead of manual YAML.

If you need it in one line:

Silver is the curated star schema, gold is what Tableau reads, cluster the big tables now and tune the rest with real query data, and copy the snapshot history once so past numbers don't change.