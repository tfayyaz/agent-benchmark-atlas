# Data Benchmarks

| Benchmark | Published (Updated) | Source | Database Platform | Database No & Size | Data Format | Task Type |
|---|---|---|---|---|---|---|
| LiveSQLBench-Base-Lite | 2025-05-28 (2026-03-02) | [Hugging Face](https://huggingface.co/datasets/birdsql/livesqlbench-base-lite) | PostgreSQL | 18 databases, 61 MB (30 MB zipped) | PostgreSQL dump (`.sql`) | Both: 180 Read(Query), 90 Write(Modify) |
| LiveSQLBench-Base-Full v1 | 2025-09-04 (2026-06-13) | [Hugging Face](https://huggingface.co/datasets/birdsql/livesqlbench-base-full-v1) | PostgreSQL | 22 databases, 90 MB (17 MB zipped) | PostgreSQL dump (`.sql`) | Both: 410 Read(Query), 190 Write(Modify) |
| LiveSQLBench-Base-Lite-SQLite | 2025-07-28 (2026-05-25) | [Hugging Face](https://huggingface.co/datasets/birdsql/livesqlbench-base-lite-sqlite) | SQLite | 18 databases, 38 MB | SQLite database files (`.sqlite`) | Both: 180 Read(Query), 90 Write(Modify) |
| LiveSQLBench-Large-v1 | 2026-03-02 (2026-03-02) | [Hugging Face](https://huggingface.co/datasets/birdsql/livesqlbench-large-v1) | PostgreSQL | 18 databases, 1.61 GB (329 MB zipped) | PostgreSQL dump (`.sql`) | Both: Read(Query), Write(Modify) (split not published) |

**How to read the table:**

- **Published (Updated):** the published date is the authors' release date. The updated date is the dataset's last change on Hugging Face.
- **Database No & Size:** the size is the uncompressed data, counted once. For PostgreSQL that is the per-table `.sql` dump files, so a loaded database will take more disk once indexes and overhead are added.
- **Task Type:** Read(Query) tasks are `SELECT`-only questions. Write(Modify) tasks, which the authors call Management tasks, change data or schema, for example `UPDATE`, `INSERT`, `CREATE VIEW` or `CREATE FUNCTION`. They are scored by checking the database afterwards.

## Breakdown

### What you download

Each PostgreSQL release has two parts:

1. **Tasks and metadata on Hugging Face.** These files have no table data:
   - tasks and knowledge base as `.jsonl`;
   - column meanings as `.json`;
   - schemas as `.txt`.
2. **Database dumps on Google Drive.** A zip of plain-text `pg_dump` files, with one folder per database. The data is loaded with `INSERT INTO …` statements, one per row.

Each database folder holds two copies of the same data:

- one `.sql` file per table, which the load scripts use;
- one `<db>_full.sql` file with the whole database in a single dump.

The sizes in the table count the per-table files only.

The SQLite release is self-contained on Hugging Face: the same metadata files plus 18 ready-to-open `.sqlite` databases.

**Ground truth is not public for any release.** The gold SQL and test cases are emailed by the authors on request (bird.bench25@gmail.com), with a separate subject line for each release.

### LiveSQLBench-Base-Lite

- **Published:** 2025-05-28 (updated on Hugging Face 2026-03-02)
- **Tasks:** [`birdsql/livesqlbench-base-lite`](https://huggingface.co/datasets/birdsql/livesqlbench-base-lite)
- **Databases:** [`livesqlbench-base-lite-dumps.zip`](https://drive.google.com/file/d/1QIGQlRKbkqApAOrQXPqFJgUg8rQ7HRRZ/view) (30 MB zipped; 61 MB of per-table dumps, 198 `.sql` files)
- **Platform:** PostgreSQL. The same 18 databases are also published in the Docker image `shawnxxh/bird-interact-postgresql:v1`.
- **Databases:** 18, with about 10 tables each and JSON/JSONB columns
- **Tasks:** 270, made up of 180 Read(Query) and 90 Write(Modify)
- **Notes:** the original release. Each database has a JSON knowledge base of business definitions that the agent has to find and apply.

### LiveSQLBench-Base-Full v1

- **Published:** 2025-09-04 (updated on Hugging Face 2026-06-13)
- **Tasks:** [`birdsql/livesqlbench-base-full-v1`](https://huggingface.co/datasets/birdsql/livesqlbench-base-full-v1)
- **Databases:** [`bird-interact-full-dumps.zip`](https://drive.google.com/file/d/1V9SFIWebi27JtaDUAScG1xE9ELbYcWLR/view) (17 MB zipped; 90 MB of per-table dumps, 288 `.sql` files, loaded with `init-databases_postgresql.sh`)
- **Platform:** PostgreSQL
- **Databases:** 22, all new (none shared with Base-Lite), with about 11 tables and 93 columns each, and about 275K rows in total
- **Tasks:** 600, all new, made up of 410 Read(Query) and 190 Write(Modify)
- **Notes:**
  - The questions are more conversational and harder to map to the tables and knowledge base. Some need several reasoning steps.
  - The databases are messier, with more many-to-many relationships.
  - The 2026-06-13 update corrected the gold SQL and test cases for about 20 tasks.

### LiveSQLBench-Base-Lite-SQLite

- **Published:** 2025-07-28 (updated on Hugging Face 2026-05-25)
- **Tasks and databases:** [`birdsql/livesqlbench-base-lite-sqlite`](https://huggingface.co/datasets/birdsql/livesqlbench-base-lite-sqlite)
- **Platform:** SQLite
- **Databases:** the same 18 as Base-Lite, as 18 `.sqlite` files totalling 38 MB. No database server is needed.
- **Tasks:** 270, made up of 180 Read(Query) and 90 Write(Modify)
- **Notes:**
  - SQLite has no custom functions, so 20+ questions were rewritten, for example to use views.
  - It has its own ground-truth file, separate from the PostgreSQL one.
  - It isn't supported by LiveSQLBench-CLI.

### LiveSQLBench-Large-v1

- **Published:** 2026-03-02 (updated on Hugging Face 2026-03-02)
- **Tasks:** [`birdsql/livesqlbench-large-v1`](https://huggingface.co/datasets/birdsql/livesqlbench-large-v1)
- **Databases:** [`postgre_table_dumps_large.zip`](https://drive.google.com/file/d/1u1L-SvJtOZGfcIST-dINw8DnGEQDMu6C/view) (329 MB zipped; 1.61 GB of per-table dumps, 1,163 `.sql` files, loaded with `init-databases_postgresql_large.sh`)
- **Platform:** PostgreSQL
- **Databases:** 18, industrial-scale, with about 54 tables and 986 columns each, and about 2M rows in total
- **Tasks:** 480, both Read(Query) and Write(Modify). The authors don't publish the split.
- **Notes:**
  - The average prompt is about 84K tokens (range 59K–121K), against 14K for Base-Full.
  - It adds "business rule drift": the knowledge base is versioned and a definition can change between versions, so the agent has to use the version the user means.
