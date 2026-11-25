note 


import os
cat  = "cap_enterprise_data_tst"
base = "/Workspace/Users/mkhan@cap.org/cap-data-lakehouse/cap_enterprise_data_integration/database_scripts"

count = 0
for sch in ["common_silver", "sodm_silver"]:
    os.makedirs(f"{base}/{sch}", exist_ok=True)
    for r in spark.sql(f"SHOW TABLES IN {cat}.{sch}").collect():
        ddl = spark.sql(f"SHOW CREATE TABLE {cat}.{sch}.{r.tableName}").first()[0]
        ddl = ddl.replace("CREATE TABLE", "CREATE TABLE IF NOT EXISTS", 1)
        with open(f"{base}/{sch}/{r.tableName}.sql", "w") as f:
            f.write(ddl + ";\n")
        count += 1
print(count, "files written")   # expect 26