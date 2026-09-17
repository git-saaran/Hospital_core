# System Architecture

## Decision

Hospital Core begins as a **modular monolith** with a single primary PostgreSQL database for clinical data. The architecture must preserve boundaries so selected domains can be extracted into services later without redesigning the whole system.

## Context

A hospital platform combines transactional clinical workflows, operational workflows, interoperability, and enterprise integrations. Starting with microservices would add distributed-systems complexity before workload and scaling boundaries are understood.

## Logical architecture

```text
Clients
  |
  v
API / BFF
  |
  v
+---------------------------------------------------+
|                  Hospital Core                    |
|                                                   |
| Identity | Organization | Patient | Practitioner  |
| Appointment | Encounter | Clinical | Diagnosis    |
| Orders | Results | Medication | Pharmacy           |
| Nursing | Inpatient | Bed Management | OT         |
| Emergency | Discharge | Billing | Insurance       |
| Audit | Interoperability                         |
+---------------------------+-----------------------+
                            |
                       PostgreSQL
                            |
                    Transactional data

Hospital Core
    |
    +--> ERPNext API        (finance, procurement, inventory, HR)
    +--> FHIR interfaces    (external clinical interoperability)
    +--> HL7 interfaces     (where required by external systems)
    +--> DICOM/PACS         (imaging integration)
```

## Data ownership

### Hospital Core owns

- Patient identity within the institution
- Patient identifiers and external identifier mappings
- Encounters and encounter state
- Clinical notes and assessments
- Diagnoses and problems
- Orders and order state
- Clinical observations and results
- Medication requests and medication-related clinical records
- Nursing records
- Admissions, transfers, discharges
- Bed and ward clinical operations
- Procedure and operating-theatre clinical records
- Clinical audit history

### ERPNext owns

- Accounting ledger and financial documents
- Procurement and purchasing
- Enterprise inventory transactions
- Supplier records used for ERP operations
- Assets
- HR and payroll
- Other ERP-specific business processes

Integration must happen through supported APIs/events, not direct SQL access to another application's tables.

## Persistence

The initial topology is:

```text
Hospital Core -> PostgreSQL (clinical source of truth)
ERPNext       -> its own Frappe/ERPNext persistence
```

The systems may temporarily share a database server for cost or operational reasons, but should use separate databases/schemas and never share tables.

## Scaling path

1. Modular monolith
2. Add background workers and an outbox/event mechanism
3. Introduce caching and read models where measurable load requires them
4. Separate analytics workloads from clinical transactions
5. Extract a bounded context into an independent service only when justified by scale, isolation, deployment cadence, technology constraints, or organizational ownership

## Infrastructure stance

The application must run on a modest VM for local and pilot deployments. Horizontal scaling should be possible by making the API layer stateless. PostgreSQL, object storage, workers, and external integrations should be independently scalable when needed.

## Security stance

Security-sensitive capabilities are cross-cutting concerns: authentication, authorization, audit, secret handling, encryption, session management, rate limiting, and tenant/hospital isolation. Clinical data access must follow least privilege.
