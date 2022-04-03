A simple, customisable table audit system for PostgreSQL implemented using triggers.

This is based off https://github.com/2ndQuadrant/audit-trigger with the following changes

1. The row data is stored in `jsonb`.
2. Logs user information from hasura's graphql-engine (accessible by `current_setting('hasura.user')`).

## Installation

Load `audit.sql` into the database where you want to set up auditing. You can do this via psql or any other tool that lets you execute sql on the database.

```bash
psql -h <db-host> -p <db-port> -U <db-user> -d <db> -f audit.sql --single-transaction
```

## Setting up triggers

Run the following sql to setup audit on a table

```sql
select audit.audit_table('author');
```

For a table in a different schema name as follows:

```sql
select audit.audit_table('shipping.delivery');
```

This sets up triggers on the given table which logs any change (insert/update/delete) into the table `audit.logged_actions`.

```sql
select * from audit.logged_actions
```

## Available options

The function `audit.audit_table` takes the following arguments:

| argument | description |
| --- | --- |
| `target_table`    | Table name, schema qualified if not on search_path |
| `audit_rows`      | Record each row change, or only audit at a statement level |
| `audit_query_text` | Record the text of the client query that triggered the audit event? |
| `ignored_cols`  | Columns to exclude from update diffs, ignore updates that change only ignored cols. |

### Examples

Do not log changes for every row

```sql
select audit.audit_table('author', false);
```

Log changes for every row but don't log the sql statement

```sql
select audit.audit_table('author', true, false);
```

Log changes for every row, log the sql statement, but don't log the data of the columns `email` and `phone_number`

```sql
select audit.audit_table('author', true, true, '{email,phone_number}');
```
