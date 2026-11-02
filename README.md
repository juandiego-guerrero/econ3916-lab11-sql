# econ3916-lab11-sql
Lab 11 Submission

# SQL for Economists — Joins, Windows and the Cardinality Trap

## Objective
I translated common pandas operations into SQL with DuckDB, checked that both returned the same rows, and looked at where the two give different answers on the same data.

## Methodology
- Translated pandas select, filter, sort, groupby and merge steps into SQL queries run with DuckDB, and compared the rows each version returned.
- Joined customers to orders with a one-to-many `LEFT JOIN`, then ran the same Welch t-test on revenue before and after the join to see how repeated rows change the result.
- Used window functions (`AVG(...) OVER (PARTITION BY ...)` and `RANK()`) to add group averages and ranks to each row without collapsing rows the way `GROUP BY` does.
- Filtered a survey table with `WHERE income != 50000` in SQL and with the same condition in pandas, and compared which rows each kept.
- Queried a public Parquet file of about 3 million NYC taxi trips over HTTP without downloading it, so DuckDB read only the columns the query needed.
- Asked an AI to translate three of my pandas steps into SQL, revised my prompt once, and compared the row counts of its queries with my pandas results.

## Key Findings
- **Cardinality trap:** The one-to-many join turned 600 customer rows into 3,612 rows. The same Welch t-test on the same revenue figures went from t = [YOUR VALUE] (p = [YOUR VALUE]) to t = [YOUR VALUE] (p = [YOUR VALUE]). Each customer was repeated once per order, so the test treated repeated rows as new observations, which shrank the standard error and moved the difference. Checking row counts after every join is the simplest way to catch this.
- **Window functions:** Window functions give each row its group's average or rank while keeping every row, which `GROUP BY` cannot do.
- **Three-valued logic:** `WHERE income != 50000` kept 3 of 5 survey rows, because a comparison with NULL is unknown and `WHERE` keeps only rows where the condition is true. The same filter in pandas kept 5. To match pandas, the SQL needs `OR income IS NULL`.
- **Remote Parquet:** DuckDB can query a large remote Parquet file directly, reading only the columns it needs instead of the whole file.
- **AI translation:** After one prompt revision, all three AI queries matched my pandas results on row count (2, 3,612 and 5 rows).
- 
