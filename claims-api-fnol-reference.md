# Claims API Reference: Submit First Notice of Loss

## Overview

The **Submit First Notice of Loss (FNOL)** endpoint lets you create a new claim record by submitting the initial loss report for a policy. Use this endpoint when a policyholder or claims intake channel reports a new incident and you need to open a claim in the system.

A successful call returns a `claimId`, which you use in all subsequent claim-related API calls (status checks, document uploads, adjuster assignment).

---

## Authentication

All requests require a bearer token in the `Authorization` header.

```
Authorization: Bearer {access_token}
```

Tokens are issued through the platform's standard OAuth 2.0 client-credentials flow. See **Authentication Overview** for details on obtaining a token.

---

## Endpoint

```
POST /v1/claims/fnol
```

---

## Request Body

| Field | Type | Required | Description |
|---|---|---|---|
| `policyNumber` | String | Yes | The policy number the claim is being filed against. |
| `lossDate` | ISO 8601 Date | Yes | The date the loss or incident occurred. |
| `lossDescription` | String | Yes | A free-text description of what happened. |
| `lossType` | Enum | Yes | One of: `COLLISION`, `FIRE`, `THEFT`, `WATER_DAMAGE`, `LIABILITY`, `OTHER`. |
| `reportedBy` | Object | Yes | Information about the person reporting the loss. See **reportedBy object** below. |
| `contactPhone` | String | No | Callback number for the reporting party, if different from the policy's contact number on file. |
| `estimatedSeverity` | Enum | No | One of: `MINOR`, `MODERATE`, `SEVERE`, `CATASTROPHIC`. If omitted, severity is determined during triage. |
| `attachmentIds` | Array | No | List of document IDs (photos, police reports) previously uploaded via the Documents API, to associate with this claim at creation. |

### `reportedBy` object

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | String | Yes | Full name of the person reporting the loss. |
| `relationshipToPolicy` | Enum | Yes | One of: `POLICYHOLDER`, `INSURED_PARTY`, `THIRD_PARTY`, `AGENT`. |
| `email` | String | No | Email address for claim status notifications. |

---

## Sample Request

```json
POST /v1/claims/fnol

{
  "policyNumber": "PA-4471203",
  "lossDate": "2026-09-18",
  "lossDescription": "Rear-end collision at a stoplight, minor bumper damage.",
  "lossType": "COLLISION",
  "reportedBy": {
    "name": "Jordan Weiss",
    "relationshipToPolicy": "POLICYHOLDER",
    "email": "jordan.weiss@example.com"
  },
  "estimatedSeverity": "MINOR"
}
```

---

## Response

A successful request returns `201 Created` with the new claim record.

| Field | Type | Description |
|---|---|---|
| `claimId` | String | Unique identifier for the newly created claim. Use this in all subsequent claim-related calls. |
| `claimNumber` | String | Human-readable claim number, suitable for display to policyholders. |
| `status` | Enum | Initial claim status. New claims are always created with status `INTAKE`. |
| `createdAt` | ISO 8601 DateTime | Timestamp when the claim record was created. |
| `assignedAdjusterId` | String or `null` | Populated if an adjuster was auto-assigned based on routing rules; `null` if assignment is pending. |

### Sample Response

```json
{
  "claimId": "clm_88a12f0c",
  "claimNumber": "2026-CL-004471",
  "status": "INTAKE",
  "createdAt": "2026-09-18T14:32:07Z",
  "assignedAdjusterId": null
}
```

---

## Error Codes

| HTTP Status | Error Code | Description |
|---|---|---|
| `400` | `INVALID_POLICY_NUMBER` | The `policyNumber` provided does not match an active policy. |
| `400` | `MISSING_REQUIRED_FIELD` | A required field was omitted; the response body identifies which field. |
| `400` | `INVALID_LOSS_DATE` | `lossDate` is in the future, or predates the policy's effective date. |
| `401` | `INVALID_TOKEN` | The bearer token is missing, expired, or invalid. |
| `403` | `INSUFFICIENT_SCOPE` | The token is valid but lacks the `claims:write` scope required for this endpoint. |
| `409` | `DUPLICATE_CLAIM_DETECTED` | A claim with a matching policy number and loss date already exists; the response includes the existing `claimId`. |
| `429` | `RATE_LIMIT_EXCEEDED` | Too many requests in a short period. Retry after the interval specified in the `Retry-After` header. |

---

## Notes

- FNOL submissions do not require a complete loss description to succeed — a brief initial description is sufficient, and claims adjusters can update it later via the Claims Update API.
- If `attachmentIds` references a document ID that doesn't exist or hasn't finished processing, the claim is still created, but the response includes a `warnings` array noting which attachments were not linked.
- Duplicate detection (`409`) compares `policyNumber` and `lossDate` only; it does not consider `lossDescription`, so two genuinely separate incidents on the same day will still trigger this check and should be submitted with a short delay or reviewed manually.

---

*This document is an original writing sample. It does not describe or disclose any real product, network, or confidential information.*
