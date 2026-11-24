
-- A) STG tables (except stg_sol_sales_order) -> d365_tst.external_staging
SELECT concat('CREATE TABLE d365_tst.external_staging.', table_name,
              ' LIKE cap_edw_tst.sodm_silver.', table_name, ';\n',
              'ALTER TABLE d365_tst.external_staging.', table_name,
              ' OWNER TO `Administrators`;') AS ddl
FROM cap_edw_tst.information_schema.tables
WHERE table_schema = 'sodm_silver' AND table_name <> 'stg_sol_sales_order'
UNION ALL
-- B) all gold tables -> sodm_silver
SELECT concat('CREATE TABLE cap_edw_tst.sodm_silver.', table_name,
              ' LIKE cap_edw_tst.sodm_gold.', table_name, ';\n',
              'ALTER TABLE cap_edw_tst.sodm_silver.', table_name,
              ' OWNER TO `Administrators`;')
FROM cap_edw_tst.information_schema.tables
WHERE table_schema = 'sodm_gold';

SHOW TABLES IN d365_tst.external_staging;   -- expect 11
SHOW TABLES IN cap_edw_tst.sodm_silver;     -- expect 1 + 25 = 26

SELECT concat('DROP TABLE cap_edw_tst.sodm_silver.', table_name, ';') AS ddl
FROM cap_edw_tst.information_schema.tables
WHERE table_schema = 'sodm_silver' AND table_name LIKE 'stg%' AND table_name <> 'stg_sol_sales_order'
UNION ALL
SELECT concat('DROP TABLE cap_edw_tst.sodm_gold.', table_name, ';')
FROM cap_edw_tst.information_schema.tables
WHERE table_schema = 'sodm_gold';
# ostep-code
Code from various chapters in OSTEP (http://www.ostep.org)

* [Introduction](intro)

## Virtualization

CPU Virtualization Chapters:
* Processes
* [Process API](cpu-api)
* Direct Execution
* CPU Scheduling
* Multi-level Feedback
* [Lottery Scheduling](cpu-sched-lottery)
* Multi-CPU Scheduling

Memory Virtualization Chapters:
* [Address Spaces](vm-intro)
* Memory API
* Address Translation
* Segmentation
* Free Space Management
* Introduction to Paging
* Translation Lookaside Buffers
* Advanced Page Tables
* Swapping: Mechanisms
* Swapping: Policies
* Complete VM Systems

## Concurrency

Concurrency Chapters:
* [Concurrency and Threads](threads-intro)
* [Threads API](threads-api)
* [Locks](threads-locks)
* Locked Data Structures
* [Condition Variables](threads-cv)
* [Semaphores](threads-sema)
* [Concurrency Bugs](threads-bugs)
* Event-based Concurrency

## Persistence

Persistence Chapters:
* I/O Devices
* Hard Disk Drives
* Redundant Disk Arrays (RAID)
* Files and Directories
* File System Implementation
* Fast File System (FFS)
* FSCK and Journaling
* Log-structured File System (LFS)
* Flash-based SSDs
* Data Integrity and Protection
* [Distributed Systems](dist-intro)
* Network File System (NFS)
* Andrew File System (AFS)

