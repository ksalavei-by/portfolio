# Merchant Classification Code Updates

*Writing sample — Client implementation article*

## In brief

The merchant classification code set is being updated to improve merchant categorization across authorization, reporting, and reconciliation workflows.

The update includes:

- Clarified descriptions for six existing classification values.
- Four new classification codes for business types that aren't adequately represented in the current code set.
- Retirement of one legacy code and introduction of two replacement codes.

The update doesn't change the use of merchant classification codes in authorization or clearing message formats, except for the valid values and their descriptions.

Clients might be affected if their systems validate, store, transform, display, or report merchant classification data. Clients that pass classification codes through without applying validation or business logic might require little or no change.

## What is changing

| Change | Description |
|---|---|
| **Updated descriptions** | Descriptions for six existing classification values are being updated. The codes remain unchanged. |
| **New codes** | Four new classification codes are being added for business types not adequately represented in the current code set. |
| **Retired code** | One legacy code is being retired and replaced by two more specific codes. The legacy code remains valid during the defined transition period. |

## Implementation considerations

### Review validation and reference data

Identify validation rules, lookup tables, database constraints, and other configuration that reference merchant classification codes.

Update these components to support the new codes before the effective date.

### Review the retiring code

Identify systems that reference the retiring code. Update these systems to support the two replacement codes before the transition period ends.

If your system receives the retiring code during the transition period, confirm that it continues to process the value as expected.

### Review downstream systems

Confirm that downstream and third-party systems can receive and process the new and updated classification values.

This might include:

- Reporting feeds
- Analytics platforms
- Reconciliation systems
- Dashboards
- Processor- or platform-hosted services

Coordinate with your processor or platform provider if client-specific configuration or deployment steps are required.

### Update documentation and training

Update internal documentation, support procedures, onboarding materials, and training that reference affected classification codes or descriptions.

## Testing considerations

Test any system that validates, stores, transforms, displays, or reports merchant classification data.

Confirm that:

- New classification codes are accepted.
- Updated descriptions display correctly where descriptions are used.
- The retiring code remains supported during the transition period.
- The two replacement codes are supported when they become available.
- Reports and reconciliation processes handle the updated values correctly.
- Downstream systems receive and process the values as expected.
- Support and operational tools display the correct classification descriptions.

If your systems pass classification codes through without applying validation or business logic, formal testing might not be required. However, confirm that no workflow depends on the retiring code remaining valid after the transition period.

## Client checklist

### Before the update

- [ ] Identify systems and processes that use merchant classification codes or descriptions.
- [ ] Review validation rules, lookup tables, and reference data.
- [ ] Add support for the new classification codes.
- [ ] Update systems that reference the retiring code.
- [ ] Confirm downstream and processor or platform-provider readiness.
- [ ] Update reporting, reconciliation, and support documentation.
- [ ] Test affected systems.

> **Note**: If you're unsure whether your systems are affected, review how your organization uses merchant classification data and coordinate with your processor or platform provider, as applicable.

---

*This document is an original writing sample. It doesn't describe or disclose any real product, network, or confidential information.*
