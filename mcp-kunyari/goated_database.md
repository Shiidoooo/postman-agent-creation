# 💾 The Goated Genius Guide to Databases

A beautiful frontend and a secure backend mean nothing if your database queries take 5 seconds to load. This is the definitive playbook for schema design, architecture, indexing, and query optimization for scalable applications.

---

## 1. Database Architecture by Scale

You don't need a clustered Kubernetes database for a to-do app, and you can't run a fintech platform on a single SQLite file.

### 🐣 Small Projects (Scripts, Internal Tools, Prototypes)
*Goal: Simplicity, zero maintenance, lowest cost.*

*   **Engine:** SQLite or a single basic instance of MySQL/MariaDB.
*   **Architecture:** Direct connection from app to DB.
*   **Backups:** Daily automated dumps to cloud storage (S3/R2).
*   **State:** Schema modifications are often done manually or via simple SQL scripts.

### 🐥 Medium Projects (SaaS, E-commerce, Dashboards)
*Goal: High availability, data integrity, and handling traffic spikes.*

*   **Engine:** PostgreSQL (The Goated standard) or MySQL (InnoDB).
*   **Architecture:**
    *   **Connection Pooling:** Use tools like PgBouncer or a proxy. Do not let 1,000 users open 1,000 direct database connections; it will crash the DB.
    *   **Caching Layer:** Introduce Redis or Memcached. Cache heavy, frequently accessed, rarely changed queries (e.g., product catalogs, settings) to offload the primary database.
*   **Migrations:** **NEVER manually run `ALTER TABLE` in production.** Use Version Controlled Migrations (e.g., Phinx, Laravel Migrations, Prisma, Flyway) so any developer can spin up the exact database schema locally.
*   **Backups:** Automated daily snapshots + Point-In-Time-Recovery (PITR) enabled.

### 🦅 Large/Enterprise Projects (High-Traffic, Distributed Systems)
*Goal: Global scale, zero downtime, microsecond latency.*

*   **Engine:** Distributed SQL (CockroachDB, Amazon Aurora), or polyglot persistence (Postgres for relational, MongoDB for documents, ElasticSearch for search).
*   **Architecture:**
    *   **Read Replicas:** 1 Primary DB handles all `INSERT/UPDATE/DELETE`. Multiple Read-Replicas handle `SELECT` queries.
    *   **Sharding / Partitioning:** Splitting a massive 100-billion-row table across multiple servers by a shard key (e.g., `tenant_id` or `region`).
    *   **Eventual Consistency:** Accepting that Read Replicas might be a few milliseconds behind the Primary.

---

## 2. Schema Design: The Foundation

### Normalization vs. Denormalization
*   **The Rule of 3NF (Third Normal Form):** By default, normalize your data to eliminate redundancy. 
    *   *Bad:* Storing the `department_name` string in the `users` table.
    *   *Goated:* Storing `department_id` in the `users` table and `JOIN`ing to a `departments` table.
*   **When to Denormalize:** For Enterprise-scale read-heavy apps, JOINs can become too expensive. It's okay to duplicate data (like caching a `total_orders` count on a user table) to save a heavy `COUNT()` query.

### Primary and Foreign Keys
*   Every table MUST have a Primary Key (Auto-incrementing `id` or UUID).
*   **Enforce Foreign Keys:** Do not just store IDs. Enforce Foreign Key constraints at the DB level (e.g., `ON DELETE RESTRICT`). This guarantees you can't delete a department if users are still assigned to it (Data Integrity).

### Soft Deletes vs. Hard Deletes
*   **Hard Delete:** `DELETE FROM users WHERE id = 1;` (Gone forever).
*   **Soft Delete (The Standard):** Add a `deleted_at` timestamp column. `UPDATE users SET deleted_at = NOW() WHERE id = 1;`. 
    *   *Why?* Data is the most valuable asset. Soft deletes allow you to restore data when an employee accidentally clicks "Delete" and maintains historical audit trails.

---

## 3. Query Optimization: Stop Killing Your Server

### 1. The "N+1" Query Problem (The Silent Killer)
This is the most common mistake Junior developers make. 

*   **The Scenario:** You want to list 50 users and their department name.
*   **The Rookie Way (N+1):**
    ```php
    $users = query("SELECT * FROM users LIMIT 50"); // 1 Query
    foreach($users as $user) {
        // This runs 50 times inside the loop! 51 total queries.
        $dept = query("SELECT name FROM departments WHERE id = " . $user['dept_id']); 
    }
    ```
*   **The Goated Way (Eager Loading / JOINs):**
    ```sql
    SELECT u.name, d.name as dept_name 
    FROM users u 
    JOIN departments d ON u.dept_id = d.id 
    LIMIT 50;
    -- Total Queries: 1
    ```

### 2. Never Use `SELECT *` in Production
*   **Why?** It pulls unnecessary data across the network, wastes RAM, prevents the DB from using "Index Only Scans", and breaks if column orders change.
*   **Do:** Explicitly define what you need: `SELECT id, first_name, email FROM users;`

### 3. Pagination Optimization
*   **Bad:** `OFFSET 100000 LIMIT 50`. The database still has to scan and count 100,000 rows before giving you the 50. It gets exponentially slower on deep pages.
*   **Goated (Keyset/Cursor Pagination):** `WHERE id > 100000 LIMIT 50`. Uses the index directly. Microsecond response time regardless of depth.

---

## 4. Indexing: The Speed Multiplier

An index is like the table of contents in a book. Without it, the database has to scan every single page (Table Scan).

*   **What to Index:**
    1.  Foreign Keys (`user_id`, `department_id`).
    2.  Columns frequently used in `WHERE` clauses (e.g., `email`, `status`).
    3.  Columns used for sorting (`created_at`).
*   **What NOT to Index:**
    1.  Columns with low cardinality (e.g., a boolean `is_active` column). The database optimizer will likely ignore the index anyway.
    2.  EVERY column. Indexes speed up `SELECT`s, but they significantly slow down `INSERT`s, `UPDATE`s, and `DELETE`s because the index must be rebuilt every time data changes.
*   **Composite Indexes:** If you often query `WHERE first_name = ? AND last_name = ?`, create a single index covering both columns (in the order they are queried), not two separate indexes.

---

## 5. ACID Properties & Transactions

When a process involves multiple related database changes, it must succeed entirely or fail entirely. 

*   **Scenario:** Transferring $100 from Alice to Bob.
    1.  Deduct $100 from Alice.
    2.  *(Server crashes here)*
    3.  Add $100 to Bob.
*   Without a transaction, the $100 disappears into the void.

**The Standard Way:**
```php
try {
    $db->beginTransaction();
    
    $db->query("UPDATE accounts SET balance = balance - 100 WHERE user = 'Alice'");
    $db->query("UPDATE accounts SET balance = balance + 100 WHERE user = 'Bob'");
    
    $db->commit(); // Save changes permanently (Atomicity)
} catch (Exception $e) {
    $db->rollBack(); // Revert everything if anything fails
}
```
**Rule:** Wrap all multi-step state-changing operations (like importing rows, transferring money, or updating mappings) in transactions.

---
*Stay Goated. Respect the Data.*
