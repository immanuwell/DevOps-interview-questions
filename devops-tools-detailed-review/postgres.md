---
title: "PostgreSQL & database concepts in general"
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
metaLinks:
  alternates:
    - 
---

# **PostgreSQL & database concepts in general**

## **Architecture**

* PostgreSQL uses a **process-based** architecture (one OS process per connection).
* Uses **WAL (Write-Ahead Log)** to guarantee durability.
* Data stored in **pages (8 KB)** inside **tablespaces**.
* **MVCC (Multi-Version Concurrency Control)** enables high concurrency without read locks.
* Uses **shared buffers** + **OS page cache** together.

---

## **MVCC**

* Each row has **xmin/xmax** transaction IDs.
* No locks for reads → avoids read contention.
* Dead tuples accumulate → require **VACUUM** to clean them.
* MVCC ensures **consistent snapshot isolation**.

---

## **Indexes**

* Most common: **B-Tree** (default).
* Others: **GIN** (full-text search, JSONB), **GiST** (geospatial), **BRIN** (very large, sequential tables), **HASH** (rarely used).
* Too many indexes slow down **writes**.
* **Reindex** needed if index bloats.

---

## **Replication**

* Native replication is **physical WAL streaming**.
* Modes:

  * **Asynchronous** (default, may lose data).
  * **Synchronous** (strong durability, adds latency).
  * **Quorum synchronous** (majority ACK).
* **Hot standby** supports read-only queries.

---

## **Backup & Restore**

* Logical backup: **pg_dump / pg_restore** → portable, slow, not point-in-time.
* Physical backup: **pg_basebackup** → replication-compatible.
* PITR (Point-In-Time Recovery) requires:

  * Base backup
  * Continuous WAL archiving
* Tools: **pgBackRest**, **wal-g**, **barman**.

---

## **Performance Optimization**

* Check slow queries with **EXPLAIN / EXPLAIN ANALYZE**.
* Key parameters:

  * `shared_buffers` (25% RAM recommended)
  * `work_mem` (per sort)
  * `maintenance_work_mem` (VACUUM, CREATE INDEX)
  * `effective_cache_size` (OS cache estimate)
  * `max_connections` (keep low; use PG bouncers)
* Enable **autovacuum** aggressively to prevent bloat.

---

## **Query Optimization**

* Avoid `SELECT *`.
* Use appropriate indexes for joins and WHERE.
* Watch **sequential scans** vs **index scans**.
* Ensure statistics are up-to-date (`ANALYZE`).

---

## **Connection Management**

* PostgreSQL processes are expensive → use pooling.
* Tools:

  * **PgBouncer** (lightweight pooling, session/transaction pooling)
  * **PgPool-II** (pooling + load balancing + HA)
* Avoid thousands of active connections → use poolers.

---

## **Locking**

* Row-level locks: `FOR UPDATE`, `FOR NO KEY UPDATE`.
* Table-level locks: `ACCESS EXCLUSIVE` during operations like ALTER TABLE.
* Long transactions → MVCC bloat → autovacuum freeze issues.

---

## **High Availability**

* Combine **replication + failover tool**:

  * **Patroni**
  * **repmgr**
  * **Stolon**
* Use **etcd/Consul** or **Kubernetes** for leader election.

---

## **Security**

* Auth methods:

  * `md5` (deprecated)
  * `scram-sha-256` (recommended)
  * `peer` (local)
  * TLS support for encrypted transport
* Row-level security (RLS) available.
* Use separate users with **least privilege**.

---

## **Logging & Monitoring**

* Enable slow query log (`log_min_duration_statement`).
* Monitor:

  * Cache hits
  * Deadlocks
  * Autovacuum activity
  * Replication lag
  * Checkpoint frequency
* Tools:

  * **pg_stat_statements**
  * **Prometheus exporters**
  * **pganalyze**
  * **pgBadger**

---

## **Common Failure Patterns**

* Autovacuum disabled → massive bloat → downtime.
* Too many connections → OOM / CPU 100%.
* Missing WAL archiving → PITR impossible.
* Long-running transactions prevent VACUUM.
* Large DELETE → bloat → prefer **partitioning** or **soft deletes**.

---

# **General Database Concepts (Production-grade)**

## **ACID**

* **Atomicity**: All or nothing.
* **Consistency**: Rules must hold before/after transaction.
* **Isolation**: Transaction visibility control.
* **Durability**: Data survives crashes (WAL).

---

## **Normalization**

* Reduce redundancy.
* Common forms: 1NF, 2NF, 3NF.
* Helps integrity; sometimes denormalize for performance.

---

## **Transactions & Isolation Levels**

* PostgreSQL isolation levels:

  * **Read Uncommitted** → treated as Read Committed
  * **Read Committed** (default)
  * **Repeatable Read**
  * **Serializable** (strongest)
* Higher isolation = fewer anomalies = more locks.

---

## **Sharding / Partitioning**

* Horizontal sharding → scale-out (complex).
* PostgreSQL native partitioning types:

  * RANGE, LIST, HASH.
* Local indexes per partition improve performance.

---

## **Consistency Models**

* **Strong consistency** → synchronous replication.
* **Eventual consistency** → async replication.
* Trade-off often related to CAP theorem.

---

## **Types of Backups**

* Full / incremental.
* Logical / physical.
* PITR with WAL.
* Verify backups with regular restore tests.

---

## **Query Patterns**

* OLTP: small, frequent transactions.
* OLAP: heavy analytical queries.
* Use correct indexing + tuning depending on workload.

---

## **Caching Layers**

* Redis / Memcached for expensive queries.
* PostgreSQL relies heavily on OS page cache.

---

## **Common Troubleshooting**

* Slow query → check `pg_stat_activity`, locks, EXPLAIN.
* Replication lag → check network/I/O/WAL generation.
* Autovacuum not running → ensure tuning.
* High load → check index usage, connection spikes, I/O waits.
