
To verify the move:

Count tables in cap_edw_tst.sodm_silver, cap_edw_tst.sodm_gold and d365_tst.external_staging, and show the owner of each table in sodm_silver.

After you upload redshift_keys:

Using table redshift_keys (columns tablename, column, sortkey, notnull), generate ALTER TABLE statements for tables in cap_edw_tst.sodm_silver: SET NOT NULL where notnull is true; CLUSTER BY the sortkey columns ordered by abs(sortkey), max 4; for dim tables add PRIMARY KEY on the first column with RELY. Only include tables that exist in sodm_silver.















-- A) 
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

