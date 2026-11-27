---
name: data-persistence
description: Design, implement and test persistence with SQL (PostgreSQL) and NoSQL (DynamoDB, MongoDB, Valkey) — access-pattern-first data modelling, repository and unit-of-work patterns, transactions and idempotency, the transactional outbox with CDC (Debezium), expand/contract migrations, Testcontainers-based tests, data validation and reconciliation, query performance, and database security. Use when adding or changing a table, collection, key design, query, migration, cache or anything that writes data and publishes events.
---

# Data persistence

## 1. Pick the store by the job, not by habit
Write the **access patterns first** (who reads or writes what, by which key, how often, and with what consistency), then choose:
- **Relational (PostgreSQL):** invariants and constraints, multi-row transactions, ad-hoc queries, idempotency keys, the outbox. The default system of record.
- **Key-value or wide-column (DynamoDB):** known access patterns at scale; single-table design with partition and sort keys and GSIs; conditional writes.
- **Document (MongoDB):** aggregates read and written whole, with evolving shapes; validate with `$jsonSchema`; design indexes from the queries.
- **In-memory (Valkey or Redis):** caches, rate limits, short-lived dedup. Never the only copy of important data.
- **Vectors:** pgvector until scale demands a dedicated store.

Record the decision and the alternatives in an ADR.

## 2. Code structure (ports and adapters)
- **Repository** per aggregate, behind a port (a `Protocol` in Python, an `interface` in TypeScript). The domain never imports a driver.
- **Unit of work** for transactions: one `async with uow:` per use case, committing explicitly and rolling back on error.
- **One contract test suite per port**, run against every adapter (in-memory, Postgres, DynamoDB, MongoDB), so the adapters are proven interchangeable.
- Async drivers only on async services (asyncpg or psycopg 3 async, Motor/PyMongo async, aioboto3, valkey-py asyncio).

## 3. Correctness patterns
- **Idempotency:** a unique constraint on the idempotency key, plus `INSERT … ON CONFLICT DO NOTHING` (or a conditional put), returning the existing result on replay.
- **No dual writes:** never "write to the database, then publish to Kafka". Use a **transactional outbox**: the business row and the outbox row go in one transaction, and **Debezium's Outbox Event Router** publishes them. The aggregate id becomes the Kafka key, which keeps per-key ordering.
- **Optimistic concurrency:** a version column with a conditional update. Reject stale writes; never use last-writer-wins.
- **IDs:** time-ordered UUIDs (`uuidv7()` in Postgres 18) for index locality.
- **Time:** store UTC (`timestamptz`), and keep both the event time and the processing time.

## 4. Migrations
- Versioned migrations (Alembic in Python, or the TypeScript tool in use), reviewed like code.
- **Expand/contract:** add the new column or table → dual-read or backfill → switch readers → remove the old one in a later release. Every migration stays backward compatible with the previous app version.
- In CI, test upgrade from empty, upgrade from the previous release, and downgrade where it's supported.

## 5. Testing
- **Testcontainers** for Postgres, MongoDB and Valkey (and the AWS emulators for DynamoDB). Use a fresh schema or database per test session, and isolate each test (a transaction rollback, or truncation).
- Test **constraints and invariants** directly: the duplicate insert is rejected, the stale update is rejected, the RLS policy hides other tenants.
- **CDC tests:** an outbox insert produces exactly one Kafka message with the right key and headers; a rollback produces nothing; a connector restart doesn't lose ordering per key.
- **Data validation:** schema validation on documents, plus property-based tests for mapping code.
- **Reconciliation:** counts and checksums between stores per time window, as a test and as a monitored job.
- Never point tests at shared or production databases.

## 6. Performance
- Index from the access patterns; check plans with `EXPLAIN (ANALYZE, BUFFERS)` on the key queries, and fail CI on regressions where practical.
- Use connection pools sized to the workers, and set statement timeouts.
- Avoid N+1 queries; batch writes; paginate with keyset pagination, not `OFFSET`.
- Include the database in k6 scenarios, and watch pool saturation, locks and replication lag.

## 7. Security
- A least-privilege role per service (no superuser in apps); a CDC user with replication rights on the outbox only.
- **Row-level security** for tenant isolation, with tests.
- Parameterized queries only, and SQL/NoSQL injection tests (including MongoDB operator injection such as `$ne`).
- TLS to managed databases; secrets from the platform store; no personal data in logs.

## Checklist
- [ ] Access patterns written down; store choice justified (ADR)
- [ ] Repository and unit of work behind a port; contract tests for each adapter
- [ ] Idempotent writes; no dual writes (outbox)
- [ ] Migration is expand/contract and tested
- [ ] Tests on Testcontainers; constraint, RLS and injection tests included
- [ ] Key queries indexed and plan-checked
