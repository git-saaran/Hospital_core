# Domain Boundaries

Hospital Core is organized around business capabilities rather than UI pages.

## Identity

Responsible for authentication-facing identities, roles, permissions, practitioner/staff identity references, and external identity mappings.

## Organization

Responsible for hospitals, facilities, departments, wards, rooms, locations, care teams, and organizational hierarchy.

## Patient

Responsible for the institution's patient master record, identifiers, demographics, contacts, and external identifier mappings.

## Appointment

Responsible for scheduling, availability, booking, cancellation, wait lists, queues, and appointment lifecycle.

## Encounter

Responsible for the clinical interaction lifecycle: arrival, start, in-progress, completed, cancelled, transferred, and related encounter context.

## Clinical

Responsible for assessments, notes, observations, problems, diagnoses, allergies, and other clinical facts.

## Orders and Results

Responsible for clinical orders, order state, specimen/result relationships, result review, and downstream integration events.

## Medication and Pharmacy

Keep clinical medication intent distinct from inventory/ERP stock concerns. Clinical prescribing and medication administration belong to Hospital Core; enterprise stock/accounting may integrate with ERPNext.

## Inpatient and Nursing

Responsible for admission, transfer, discharge, bed allocation, nursing observations, care plans, medication administration records, and inpatient workflow.

## Procedures and OT

Responsible for procedure planning, perioperative records, operating-room workflow, surgical documentation, and clinical outcomes.

## Emergency

Responsible for emergency arrival, triage, acuity, emergency encounters, disposition, and conversion to inpatient/outpatient care.

## Billing and Insurance

Hospital Core should retain the clinical/charge context. Financial posting, ledger, and ERP documents are integrated with ERPNext through APIs/events.

## Audit

Responsible for immutable or append-only audit records for sensitive clinical and administrative changes.

## Interoperability

Responsible for FHIR/HL7/DICOM adapters, external identifier mapping, message delivery, retries, and integration observability.

## Boundary rule

A module owns its domain behavior. Other modules should communicate through explicit application interfaces/domain events rather than direct access to internal tables.
