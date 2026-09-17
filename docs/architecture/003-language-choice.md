# Backend Language Choice

## Decision

The initial Hospital Core backend will use **Go** as the primary application language.

Python remains an intentional secondary language for data science, AI/ML, clinical analytics, scripting, and specialized integration workers where its ecosystem provides a clear advantage.

## Why Go for the core

Hospital Core is a long-lived transactional system. Its primary workload is API requests, database transactions, background jobs, interoperability, and concurrent I/O rather than numerical computing.

Go provides a small runtime footprint, native concurrency primitives, a compiled binary, strong static typing, and a standard library suited to HTTP services and database-backed applications. Go's `database/sql` also provides transaction support, cancellation through `context.Context`, and connection pooling for concurrent database access.

## Recommended stack

- **Backend:** Go
- **HTTP:** standard `net/http` initially; add a router only when it materially improves maintainability
- **Database:** PostgreSQL
- **Database access:** `database/sql` or a deliberate SQL/query-generation layer
- **Migrations:** versioned SQL migrations
- **Cache:** Redis when required
- **Async work:** background workers plus an outbox pattern
- **API contract:** OpenAPI
- **Interoperability:** FHIR/HL7/DICOM adapters as separate modules
- **Frontend:** Next.js/TypeScript
- **AI/analytics:** Python services or jobs when justified

## Architectural rule

Language choice must not determine domain boundaries. Keep the Hospital Core a modular monolith first. A future service extraction should preserve domain contracts regardless of whether the extracted service remains in Go or uses another language.

## Why not make everything Python

Python is fully capable of powering a production hospital API. FastAPI is designed for high-performance APIs, and Python's async ecosystem is well suited to I/O-bound workloads. However, the core platform would benefit from Go's single compiled deployment artifact, static type system, low runtime overhead, and straightforward concurrency model.

Python should still be used where it is the better tool, especially for AI/ML and analytical workloads.

## Why not split Go and Python into microservices now

Doing so would create distributed-systems and deployment complexity before there is evidence that independent service scaling is necessary. The first architecture should therefore be one Go modular monolith with clean internal package boundaries.

## Revisit criteria

Re-evaluate the decision if:

- a concrete subsystem requires a Python-first ecosystem;
- measured workload shows a different runtime is materially better for a bounded domain;
- organizational ownership makes another language operationally preferable;
- a service extraction creates a clear independent deployment boundary.
