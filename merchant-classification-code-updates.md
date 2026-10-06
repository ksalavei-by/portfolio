# Merchant Classification Code Updates

*Writing sample: Client Implementation Notice*

---

## Purpose

This article notifies clients of upcoming updates to the merchant classification code set and describes the actions clients should take to prepare their systems and processes.

## Scope

This article applies to any client system that validates, stores, transforms, displays, or reports merchant classification data, including acquiring platforms, issuing platforms, reporting tools, and reconciliation systems.

## Summary of Change

The merchant classification code set is being updated to improve merchant categorization across authorization, reporting, and reconciliation workflows. The update comprises three changes:

- Clarified descriptions for six existing classification values.
- Introduction of four new classification codes for business types not adequately represented in the current code set.
- Retirement of one legacy code, replaced by two new, more specific codes.

This update does not change the use of merchant classification codes within authorization or clearing message formats, other than the valid values and their descriptions.

## Detailed Description of Change

### Updated Descriptions

Descriptions for six existing classification values are being updated. The codes themselves remain unchanged.

**Acquirer impact.** Acquirers must be aware that the displayed and stored descriptions for six existing classification codes will change, even though the underlying code values stay the same.

**Issuer impact.** Issuers of the impacted systems must be aware that any reporting, statements, or account management screens showing classification descriptions may need to reflect the updated wording, even though no code values are changing.

### New Codes

Four new classification codes are being added for business types not adequately represented in the current code set.

**Acquirer impact.** Acquirers must be aware that the four new classification codes must be accepted during merchant onboarding and boarding validation, and made available wherever a classification code is selected or assigned.

**Issuer impact.** Issuers of the impacted systems must be aware that the four new classification codes may appear in incoming transaction and reporting data and must be recognized rather than rejected, defaulted, or flagged as invalid.

### Retired Code

One legacy code is being retired and replaced by two more specific codes. The legacy code remains valid during the defined transition period specified in the Client Checklist below.

**Acquirer impact.** Acquirers must be aware that the legacy code should no longer be assigned to new or reclassified merchants once its two replacement codes are available, even though it remains valid for existing merchants for the duration of the transition period.

**Issuer impact.** Issuers of the impacted systems must be aware that they must add support for the two replacement codes before the transition period ends, since the legacy code will no longer be accepted once that period closes.

## Determining Applicability

Clients should review systems and processes that:

- Validate merchant classification codes against a fixed list.
- Store merchant classification codes or descriptions.
- Transform or map classification values.
- Display classification codes or descriptions to users.
- Use classification values in reports, dashboards, or analytics.
- Use classification values in reconciliation processes.
- Provide classification information to support or operations teams.

Clients whose systems treat classification codes as pass-through data, without applying validation or business rules, may require little or no system change, but should still confirm their systems can process the updated values.

## Implementation Requirements

### Validation and Reference Data

Clients must identify validation rules, lookup tables, database constraints, and other configuration referencing merchant classification codes, and update these components to support the new codes before the effective date specified in the header of this notice.

### Retiring Code

Clients must identify systems referencing the retiring code and update these systems to support the two replacement codes before the transition period ends. Systems receiving the retiring code during the transition period must continue to process the value as expected.

### Downstream Systems

Clients must confirm that downstream and third-party systems can receive and process the new and updated classification values, including:

- Reporting feeds
- Analytics platforms
- Reconciliation systems
- Dashboards
- Processor- or platform-hosted services

Clients should coordinate with their processor or platform provider where client-specific configuration or deployment steps are required.

### Documentation and Training

Clients must update internal documentation, support procedures, onboarding materials, and training referencing affected classification codes or descriptions.

## Testing Requirements

Clients must test any system that validates, stores, transforms, displays, or reports merchant classification data, and confirm that:

- New classification codes are accepted.
- Updated descriptions display correctly wherever descriptions are used.
- The retiring code remains supported during the transition period.
- The two replacement codes are supported from the effective date.
- Reports and reconciliation processes handle the updated values correctly.
- Downstream systems receive and process the values as expected.
- Support and operational tools display the correct classification descriptions.

Clients whose systems pass classification codes through without applying validation or business logic may not require formal testing, but must confirm that no workflow depends on the retiring code remaining valid after the transition period.

## Client Checklist

### Before the Effective Date

- [ ] Identify systems and processes that use merchant classification codes or descriptions.
- [ ] Review validation rules, lookup tables, and reference data.
- [ ] Add support for the new classification codes.
- [ ] Update systems that reference the retiring code.
- [ ] Confirm downstream and processor- or platform-provider readiness.
- [ ] Update reporting, reconciliation, and support documentation.
- [ ] Complete testing of affected systems.

### After the Effective Date

- [ ] Monitor for validation errors or rejected classification values.
- [ ] Confirm that reports and reconciliation processes reflect the updated values correctly.
- [ ] Monitor use of the retiring code during the transition period.
- [ ] Resolve unexpected processing or reporting issues.
- [ ] Retain implementation and testing records per your organization's procedures.

## Additional Guidance

This notice provides general implementation guidance for client planning and testing and does not replace an organization's own technical analysis, testing procedures, or release-readiness process.

Clients uncertain whether they are affected should review their use of merchant classification data and, where applicable, coordinate with their processor or platform provider.

---

*This document is an original writing sample. It doesn't describe or disclose any real product, network, or confidential information.*
