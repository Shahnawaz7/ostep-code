DESCRIBE CATALOG EXTENDED cap_edw_tst;   -- copy the Storage Root
SHOW GRANTS ON CATALOG cap_edw_tst;      -- note who has access

CREATE CATALOG IF NOT EXISTS cap_edlh_tst MANAGED LOCATION '<storage root from step 1>';
ALTER CATALOG cap_edlh_tst OWNER TO `Administrators`;
CREATE SCHEMA IF NOT EXISTS cap_edlh_tst.edlh_silver;
CREATE SCHEMA IF NOT EXISTS cap_edlh_tst.sodm_silver;
CREATE SCHEMA IF NOT EXISTS cap_edlh_tst.sodm_gold;

SELECT concat_ws('\n', collect_list(concat(
  'CREATE TABLE cap_edlh_tst.',
  CASE WHEN table_name IN ('dim_code','dim_contact','dim_country','dim_customer_attribute',
    'dim_customer_site','dim_data_source','dim_date','dim_edw_control','dim_edw_process',
    'dim_gl_coa','dim_laboratory','dim_location','dim_organization',
    'dim_product_attribute','dim_week') THEN 'edlh_silver.' ELSE 'sodm_silver.' END,
  table_name, ' LIKE cap_edw_tst.sodm_silver.', table_name, ';'))) AS ddl
FROM cap_edw_tst.information_schema.tables
WHERE table_schema = 'sodm_silver';

SELECT concat_ws('\n', collect_list(concat('ALTER TABLE cap_edlh_tst.', table_schema, '.', table_name,
  ' OWNER TO `Administrators`;'))) AS ddl
FROM cap_edlh_tst.information_schema.tables
WHERE table_schema IN ('edlh_silver','sodm_silver');

SHOW TABLES IN cap_edlh_tst.edlh_silver;   -- 15
SHOW TABLES IN cap_edlh_tst.sodm_silver;   -- 11