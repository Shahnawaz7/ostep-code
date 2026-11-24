Notes for Monday's review

• fct_sol_sales_order clusters on customer_number, customer_group, agreement_id, which is the Redshift sort key. Most queries filter on cap_program_year, so ask Isaac whether to add it as the first cluster column.
• stg_sol_sales_order clusters on order number + line number. That's good, because the MERGE into the fact table joins on exactly those columns.
• fct_or_snapshot_detail is included, but the snapshot-history question is still open; raise that too.
• Small DIM tables get clustering, which is harmless.