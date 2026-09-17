# Hospital Core

An institution-grade, interoperable hospital clinical platform built as a modular monolith first, with clear boundaries for future service extraction.

## Vision

Hospital Core is the clinical system of record for patient-centered care. It is intentionally separated from ERP responsibilities so the clinical domain can evolve independently while integrating with ERPNext and external healthcare systems.

## Architectural principles

1. **Clinical domain first** — patient, encounter, clinical documentation, orders, results, medication, nursing, inpatient care, procedures, and discharge belong to Hospital Core.
2. **Modular monolith first** — keep one deployable application and one primary clinical PostgreSQL database initially. Extract services only when there is a demonstrated scaling, isolation, or ownership reason.
3. **API-first** — all important workflows are exposed through versioned APIs rather than direct database coupling.
4. **Interoperability by design** — use FHIR/HL7/DICOM at system boundaries where appropriate instead of making proprietary integrations the core model.
5. **Auditability** — clinical mutations must be attributable, timestamped, and reviewable.
6. **ERP separation** — ERPNext remains an enterprise/ERP system of integration for finance, procurement, inventory, HR, and related workflows. Hospital Core must not directly manipulate ERPNext tables.
7. **Security and privacy** — least privilege, strong identity, tenant/hospital boundaries, encryption, secure secrets handling, and defensible audit trails are first-class requirements.
8. **Event-ready** — domain events and an outbox pattern should allow asynchronous integrations without forcing distributed systems complexity on day one.

## Initial domain map

```text
identity/
organization/
patient/
practitioner/
appointment/
encounter/
clinical/
diagnosis/
orders/
laboratory/
radiology/
medication/
pharmacy/
nursing/
inpatient/
bed-management/
operating-theatre/
emergency/
discharge/
billing/
insurance/
inventory/
audit/
interoperability/
```

## System boundary

```text
                    +----------------------+
                    |      Next.js UI      |
                    +----------+-----------+
                               |
                         Hospital API
                               |
                    +----------v-----------+
                    |     Hospital Core     |
                    |   Modular Monolith    |
                    +----------+-----------+
                               |
                         PostgreSQL
                               |
                    +----------v-----------+
                    | Interoperability/API  |
                    +----+---------+--------+
                         |         |
                      ERPNext    LIS/PACS
```

## Repository status

This repository starts with architecture and domain-contract work. Implementation should be incremental and testable.

## Near-term milestones

- [ ] Architecture decision records (ADRs)
- [ ] Domain glossary and bounded-context definitions
- [ ] Identity and organization model
- [ ] Patient master and identifiers
- [ ] Encounter lifecycle
- [ ] Clinical documentation model
- [ ] Orders/results model
- [ ] Audit/event model
- [ ] API conventions and versioning
- [ ] FHIR mapping strategy
- [ ] ERPNext integration contract
- [ ] Local development stack
- [ ] CI, security, quality, and migration checks

## Non-goals for the first milestone

- No Kubernetes requirement
- No microservice-per-domain architecture
- No direct ERPNext database coupling
- No premature Kafka/NATS dependency
- No UI-first implementation without domain contracts
