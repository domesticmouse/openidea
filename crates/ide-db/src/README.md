# src

## Overview
Database tool window backend supporting SQLite, PostgreSQL, MySQL/MariaDB, and SQL Server: data sources, SSH-tunneled connection pools, catalog introspection, DDL generation, query execution, execution plans, and table editing.

## Interface
- [lib.rs:DbManager] — Manages per-project data sources, connection pools, SSH tunnels, manual-commit transactions, schema introspection, query execution, explain plans, and table row edits (`new`, `sources`, `source`, `save_sources`, `set_password`, `set_ssh_secret`, `trust_ssh_host`, `test`, `is_connected`, `connected`, `pool`, `disconnect`, `disconnect_source`, `databases`, `schemas`, `tables`, `table_details`, `schema_objects`, `object_ddl`, `ddl`, `completion`, `execute`, `history`, `remove_history`, `explain`, `set_manual_commit`, `finish_transaction`, `table_page`, `apply_edits`).
- [lib.rs:DbTableKind] / [lib.rs:DbObjectKind] / [lib.rs:TriggerInfo] / [lib.rs:RoutineInfo] / [lib.rs:SequenceInfo] / [lib.rs:SchemaObjects] / [lib.rs:TableInfo] / [lib.rs:ColumnInfo] / [lib.rs:IndexInfo] / [lib.rs:TableDetails] / [lib.rs:CompletionColumn] / [lib.rs:CompletionTable] / [lib.rs:ForeignKey] / [lib.rs:CompletionModel] / [lib.rs:TablePage] / [lib.rs:Databases] / [lib.rs:split_target] — Introspection, completion, and paging models.
- [conn.rs:Pool] / [conn.rs:Held] / [conn.rs:QueryResult] / [conn.rs:ResultColumn] — Multi-driver connection pool (`sqlx` for SQLite/Postgres/MySQL, `bb8` + `tiberius` for SQL Server) and query execution result.
- [source.rs:DataSource] / [source.rs:DbDriver] / [source.rs:DbSslMode] / [source.rs:DbColor] / [source.rs:DbTxMode] / [source.rs:DbOptions] / [source.rs:DbIntrospection] / [source.rs:DbProperty] / [source.rs:DbSsh] / [source.rs:DbSshAuth] / [source.rs:load] / [source.rs:save] — `.openidea/data-sources.toml` configuration model.
- [sql.rs:CellValue] / [sql.rs:DbRowEdit] / [sql.rs:SortKey] / [sql.rs:quote_ident] / [sql.rs:quote_literal] / [sql.rs:qualified] / [plan.rs:PlanNode] / [plan.rs:PlanProperty] / [plan.rs:explain_statement] / [plan.rs:parse] / [split.rs:SqlStatement] / [split.rs:split] / [split.rs:statement_at] / [split.rs:statements_in] / [history.rs:HistoryStore] / [history.rs:HistoryEntry] / [secrets.rs:SecretStore] — Dialect SQL builders, `EXPLAIN` parser, UTF-16 SQL splitter, query history, and secret storage.

## Invariants
- [source.rs:DataSource] / [secrets.rs:SecretStore::write] — Passwords and SSH secrets are never written to `.openidea/data-sources.toml`; saved secrets are stored in an app-wide JSON file with mode `0o600` on Unix.
- [split.rs:SqlStatement] — Statement `from` and `to` offsets are UTF-16 code units, respecting dialect string escapes, comments, backticks (MySQL), and `$tag$` dollar-quoting (PostgreSQL).
- [history.rs:MAX_ENTRIES] / [lib.rs:MAX_META_ROWS] — Caps stored query history at 500 deduplicated entries per data source and catalog metadata queries at 100,000 rows.

## Side Effects
- [conn.rs:Pool::connect] / [lib.rs:DbManager::endpoint] — Opens TCP/Unix/file database connections (`sqlx` / `tiberius`) and shared SSH port-forwarding tunnels via `ide_ssh::SshManager`.
- [source.rs:save] / [secrets.rs:SecretStore::write] / [history.rs:HistoryStore::write] — Writes `.openidea/data-sources.toml` in the project directory and atomic `.json.tmp` -> `.json` files for saved passwords and query history in the app data directory.

## Verification
- Run: `cargo test -p ide-db`
