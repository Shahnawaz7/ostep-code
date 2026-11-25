• UAT repointed: the UAT informix-delta-sync job now uses /cap/apps/databricks/scores/uat/* (UAT SCORES, scores_int_db). Previously it used the unsuffixed /scores/* params, which point to TST. A test run on one table (ptt_ee_sc_prt_seq) succeeded.
• Schedule still paused: full runs have been failing since Sep 30, unrelated to the SSM change:
  1. row_count_report fails because the list of 502 table names passed from select_tables exceeds Databricks' 10,000-character task-value limit.
  2. 3 of 502 tables failed in sync_tables on Oct 4 (cause to be checked).
@Ashwin, is the row_count_report issue already fixed in the TST/Prod version? If so, we can redeploy UAT from develop and resume the schedule.
• Next: create /scores/tst/* params with the current TST values, repoint the TST job to them, then retire the unsuffixed /scores/* params once nothing uses them.

Thanks,
Mohammed