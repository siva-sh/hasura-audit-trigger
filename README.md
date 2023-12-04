A simple, customisable table audit system for PostgreSQL implemented using triggers.

This is based off https://github.com/2ndQuadrant/audit-trigger with the following changes
[Here's more information about this project](http://wiki.postgresql.org/wiki/Audit_trigger_91plus)

1. The row data is stored in `jsonb`.
2. Logs user information from hasura's graphql-engine (accessible by `current_setting('hasura.user')`).
3. **Every table exposes a primary key column with a universally unique identifier __id_. This __id_ is used to track row changes across tables.**


# Forks

* https://github.com/iloveitaly/audit-trigger - [contains a bunch of PRs](https://github.com/2ndQuadrant/audit-trigger/pull/48) including using jsonb for storage, function to stop auditing a table, option to not track inserts, and including pk ids in the audit table.

## Installation

Load `audit.sql` into the database where you want to set up auditing. You can do this via psql or any other tool that lets you execute sql on the database.

```bash
psql -h <db-host> -p <db-port> -U <db-user> -d <db> -f audit.sql --single-transaction
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
select audit.audit_table('public.author', false);
```

Log changes for every row but don't log the sql statement

```sql
select audit.audit_table('public.author', true, false);
```

Log changes for every row, log the sql statement, but don't log the data of the columns `email` and `phone_number`

```sql
select audit.audit_table('public.author', true, true, '{email,phone_number}');
```

View the list of tables currently being audited

```sql
select * from audit.tableslist
```

Remove auditing from a table currently being audited

```sql
select deaudit_table('public.author')
```
