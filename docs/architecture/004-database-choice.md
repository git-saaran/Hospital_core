# Database Choice

## Decision

The Hospital Core will use **PostgreSQL** as its primary transactional database and clinical source of truth.

ERPNext/Frappe will retain its own persistence. The Hospital Core must not directly read from or write to ERPNext tables.

## Why PostgreSQL

Hospital Core requires strong transactional guarantees for patient, encounter, order, medication, admission, transfer, discharge, billing-reference, and audit workflows.

PostgreSQL provides the relational model, ACID transactions, constraints, indexes, JSON/JSONB support, robust concurrency control, mature tooling, and a broad ecosystem needed for a long-lived healthcare platform.

## Data model principles

- Use relational tables for core clinical and operational entities.
- Use foreign keys and database constraints for integrity-critical relationships.
- Prefer explicit, typed columns for important clinical fields rather than putting the whole model into JSON.
- Use JSON/JSONB for genuinely flexible or externally sourced payloads, not as a substitute for relational modeling.
- Use UUID/ULID-style opaque identifiers where appropriate; never expose sequential database IDs as a security boundary.
- Store timestamps with timezone awareness and define a consistent UTC persistence policy.
- Maintain immutable/auditable history for clinically significant changes.
- Treat migrations as versioned application artifacts.

## Initial topology

```text
Hospital Core application
        |
        v
PostgreSQL
  |
  +-- clinical transactional data
  +-- operational hospital data
  +-- audit/event outbox data

ERPNext/Frappe
        |
        v
Its own database/persistence
```

The two systems may initially run on the same database server for cost or operational simplicity, but they must use separate databases/schemas and never share tables.

## Supporting data systems

PostgreSQL is the system of record, not necessarily the system for every workload.

Use additional stores only when there is a demonstrated requirement:

- **Redis:** cache, ephemeral state, rate limiting, queues where appropriate
- **Object storage:** clinical documents, images, exports, large attachments
- **Search engine:** only if PostgreSQL full-text/search capabilities are insufficient
- **Analytics warehouse/column store:** keep heavy reporting away from the clinical OLTP workload
- **Event broker:** introduce when asynchronous integration/event volume justifies it

## Multi-hospital strategy

The initial domain model must support an institution hierarchy without prematurely forcing database-per-hospital deployment.

A common starting model is:

```text
Organization
  |
  +-- Hospital / Facility
       |
       +-- Campus
            |
            +-- Department
                 |
                 +-- Location / Ward / Room / Bed
```

Tenant/isolation rules must be enforced at the application and authorization layers, with database constraints and query patterns designed to make accidental cross-tenant access difficult.

## Scaling path

1. Start with one PostgreSQL instance for the Hospital Core.
2. Add proper indexes, connection pooling, query observability, and backups.
3. Separate read-heavy reporting through replicas/read models when required.
4. Move analytics to a separate analytical store when OLTP/reporting contention becomes measurable.
5. Move to managed/high-availability PostgreSQL or a PostgreSQL cluster when availability and scale requirements justify it.
6. Partition or archive very large time-series/audit tables only when measured data volume warrants it.

## Backup and recovery

Clinical databases require tested backup and recovery procedures. The implementation must define:

- automated backups;
- point-in-time recovery where supported;
- encrypted backup storage;
- retention policies;
- restore testing;
- recovery point objective (RPO);
- recovery time objective (RTO).

## Architectural rule

Do not choose a database based on the expectation that the Hospital Core will eventually become microservices. A clean PostgreSQL domain model can support a modular monolith first and service extraction later.
