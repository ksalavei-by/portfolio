# Claims API reference: Submit First Notice of Loss

## Overview

Use the **Submit First Notice of Loss (FNOL)** endpoint to create a claim record from an initial loss report.

A successful request returns a `claimId`. Use this identifier in subsequent claim API requests, such as requests to check claim status, upload documents, or assign an adjuster.

## Authentication

Authenticate requests by including a bearer token in the `Authorization` header.

```http
Authorization: Bearer {access_token}
```

Tokens are issued through the platform's OAuth 2.0 client credentials flow. For more information, see **Authentication Overview**.

## Endpoint

```http
POST /v1/claims/fnol
```

## Request

Include the following properties in the request body.

| Property | Type | Required | Description |
|---|---|---|---|
| `policyNumber` | `string` | Yes | Policy number associated with the claim. |
| `lossDate` | ISO 8601 date | Yes | Date on which the loss or incident occurred. The date can't be in the future or before the policy's effective date. |
| `lossDescription` | `string` | Yes | Initial description of the loss or incident. A brief description is sufficient. |
| `lossType` | `enum` | Yes | Type of loss. Valid values are `COLLISION`, `FIRE`, `THEFT`, `WATER_DAMAGE`, `LIABILITY`, and `OTHER`. |
| `reportedBy` | `object` | Yes | Information about the person reporting the loss. |
| `contactPhone` | `string` | No | Callback number for the reporting party. Use this property if the number differs from the policy contact number. |
| `estimatedSeverity` | `enum` | No | Estimated severity of the loss. Valid values are `MINOR`, `MODERATE`, `SEVERE`, and `CATASTROPHIC`. If omitted, severity is determined during triage. |
| `attachmentIds` | `array` of `string` | No | IDs of documents previously uploaded through the Documents API. The documents are associated with the claim when the claim is created. |

### `reportedBy` object

| Property | Type | Required | Description |
|---|---|---|---|
| `name` | `string` | Yes | Full name of the person reporting the loss. |
| `relationshipToPolicy` | `enum` | Yes | Relationship of the reporting party to the policy. Valid values are `POLICYHOLDER`, `INSURED_PARTY`, `THIRD_PARTY`, and `AGENT`. |
| `email` | `string` | No | Email address for claim status notifications. |

### Request example

```http
POST /v1/claims/fnol
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "policyNumber": "PA-4471203",
  "lossDate": "2026-09-18",
  "lossDescription": "Rear-end collision at a stoplight, minor bumper damage.",
  "lossType": "COLLISION",
  "reportedBy": {
    "name": "Alex Morgan",
    "relationshipToPolicy": "POLICYHOLDER",
    "email": "alex.morgan@example.com"
  },
  "estimatedSeverity": "MINOR"
}
```

## Response

A successful request returns `201 Created` and the newly created claim record.

| Property | Type | Description |
|---|---|---|
| `claimId` | `string` | Unique identifier for the claim. Use this identifier in subsequent claim API requests. |
| `claimNumber` | `string` | Human-readable claim number for display to policyholders. |
| `status` | `enum` | Initial claim status. A new claim has a status of `INTAKE`. |
| `createdAt` | ISO 8601 date-time | Date and time when the claim record was created. |
| `assignedAdjusterId` | `string` or `null` | ID of the automatically assigned adjuster. This property is `null` if assignment is pending. |
| `warnings` | `array` | Warnings generated while creating the claim. This property is returned when one or more referenced attachments couldn't be linked. |

### Response example

```http
HTTP/1.1 201 Created
Content-Type: application/json
```

```json
{
  "claimId": "clm_88a12f0c",
  "claimNumber": "2026-CL-004471",
  "status": "INTAKE",
  "createdAt": "2026-09-18T14:32:07Z",
  "assignedAdjusterId": null
}
```

## Error responses

The endpoint can return the following HTTP status codes and error codes.

| HTTP status | Error code | Description |
|---|---|---|
| `400 Bad Request` | `INVALID_POLICY_NUMBER` | The `policyNumber` doesn't match an active policy. |
| `400 Bad Request` | `MISSING_REQUIRED_FIELD` | A required property is missing. The response identifies the missing property. |
| `400 Bad Request` | `INVALID_LOSS_DATE` | `lossDate` is in the future or is before the policy's effective date. |
| `401 Unauthorized` | `INVALID_TOKEN` | The bearer token is missing, expired, or invalid. |
| `403 Forbidden` | `INSUFFICIENT_SCOPE` | The token is valid but doesn't include the `claims:write` scope required by this endpoint. |
| `409 Conflict` | `DUPLICATE_CLAIM_DETECTED` | A claim with the same policy number and loss date already exists. The response includes the existing `claimId`. |
| `429 Too Many Requests` | `RATE_LIMIT_EXCEEDED` | The request rate exceeded the allowed limit. Retry after the interval specified in the `Retry-After` header. |

## Processing notes

### Loss descriptions

The `lossDescription` property must contain an initial description of the incident. A brief description is sufficient. Claims adjusters can update the description later through the Claims Update API.

### Attachments

If an `attachmentIds` value references a document that doesn't exist or hasn't finished processing, the claim is still created. The response includes a `warnings` array that identifies attachments that weren't linked.

### Duplicate claims

The duplicate check compares `policyNumber` and `lossDate`. It doesn't compare `lossDescription` or other request properties.

As a result, two separate incidents involving the same policy on the same date can trigger `DUPLICATE_CLAIM_DETECTED`. If the incidents are separate, review the existing claim before submitting another FNOL request.

## Related topics

- Authentication Overview
- Claims Update API
- Documents API
- Claim status API
- Assign an adjuster

---

*This document is an original writing sample. It doesn't describe or disclose any real product, network, or confidential information.*
