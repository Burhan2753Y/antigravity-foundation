---
trigger: model_decision
description: Activate this context only when writing SQL queries, designing database schemas, or editing files with extensions like .sql or .ddl.
---

## SQL & Database Guidelines

- **Query Writing:**
  - Use capitalized keywords (SELECT, FROM, WHERE) for readability.
  - Always use parameterized queries or prepared statements to prevent SQL injection.
  - Avoid `SELECT *`; explicitly list the columns you need.

- **Performance:**
  - Ensure columns used in `WHERE`, `JOIN`, and `ORDER BY` clauses are indexed appropriately.
  - Use `EXPLAIN` to analyze query performance on complex joins.

- **Schema:**
  - Use `snake_case` for table and column names.
  - Enforce foreign key constraints to maintain referential integrity.
  - Default to `IST` for all timestamp columns.