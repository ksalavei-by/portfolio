# Implementation Guide: Portfolio Rebalancing Drift Monitoring

**Document Type:** Implementation Guide (Writing Sample)
**Distribution:** Portfolio Managers, Portfolio Operations, Client Implementation Teams
**Author:** Katsiaryna Salavei
**Status:** Portfolio writing sample

---

## 1. Summary of Change

This release introduces **automated drift monitoring** for portfolio rebalancing, allowing portfolio managers to see, in real time, how far a portfolio's actual asset allocation has moved from its target allocation and to receive an alert when that drift crosses a configurable threshold.

Previously, allocation drift was reviewed manually, typically on a fixed schedule (e.g., weekly or monthly), meaning a portfolio could sit meaningfully out of alignment with its target allocation between review cycles. With this enhancement, drift is calculated continuously as market values change, and portfolio managers are notified as soon as a position or asset class breaches its allowed tolerance band.

This change affects **portfolio managers** who set target allocations and respond to drift alerts, **portfolio operations teams** who execute rebalancing trades, and **client implementation teams** who configure allocation targets and tolerance bands during onboarding.

---

## 2. Business Impact

| Audience | Impact |
|---|---|
| **Portfolio Managers** | Receive real-time drift alerts instead of relying on scheduled review cycles; can respond to allocation drift sooner. |
| **Portfolio Operations** | May see an increase in ad hoc rebalancing trade requests, since alerts can now arrive between scheduled review dates. |
| **Client Implementation Teams** | Must configure target allocations and tolerance bands per portfolio (or per model) before drift monitoring can produce meaningful alerts. |

**Why this matters:** Allocation drift left unaddressed between review cycles can shift a portfolio's actual risk profile away from what was agreed with the client or investment committee. Continuous monitoring reduces the time a portfolio can remain out of tolerance, without requiring operations teams to check every portfolio manually.

---

## 3. Current Behavior vs. New Behavior

### Current Behavior
- Target allocations and tolerance bands are defined once, at portfolio setup, and rarely revisited outside scheduled reviews.
- Drift is calculated as part of a scheduled batch report (commonly weekly), and reviewed manually by the portfolio manager or an operations analyst.
- A portfolio that drifts outside tolerance shortly after a review may not be flagged again until the next scheduled cycle.

### New Behavior
- Drift is recalculated continuously as security prices and portfolio positions update throughout the trading day.
- When any asset class or position exceeds its configured tolerance band, an alert is generated and routed to the portfolio manager of record.
- Scheduled batch drift reports remain available and continue to serve as a periodic summary view, alongside the new real-time alerts.

---

## 4. Drift Monitoring Configuration

The following fields are introduced in the new `AllocationTarget` configuration object, set per portfolio or per model:

| Field Name | Type | Required | Description |
|---|---|---|---|
| `assetClass` | String | Yes | The asset class or sleeve being monitored (e.g., "Equity - Developed Markets"). |
| `targetWeight` | Decimal (0.0–1.0) | Yes | The target allocation weight for this asset class. |
| `toleranceBand` | Decimal (0.0–1.0) | Yes | The allowed drift, expressed as a percentage of target weight, before an alert is triggered. |
| `alertRecipients` | Array | No | List of user IDs to notify; defaults to the portfolio manager of record if left empty. |

**Sample drift alert payload:**

```json
{
  "portfolioId": "prt_50213",
  "assetClass": "Equity - Emerging Markets",
  "targetWeight": 0.15,
  "currentWeight": 0.192,
  "toleranceBand": 0.20,
  "driftStatus": "BREACHED"
}
```

Note: in the example above, the tolerance band is expressed relative to the target weight (0.20 = allowed to drift up to 20% above or below the 0.15 target, i.e., between 0.12 and 0.18). The current weight of 0.192 exceeds that upper bound, triggering the alert.

---

## 5. Implementation Requirements

### For Portfolio Managers
1. Review and confirm target allocations and tolerance bands for each portfolio or model under management.
2. Decide whether default alert routing (to the portfolio manager of record) is sufficient, or whether specific alerts should also route to an analyst or assistant.
3. Establish an internal response protocol for drift alerts — for example, whether every alert requires same-day action or can be batched for the next scheduled rebalancing window.

### For Portfolio Operations
- Be prepared for rebalancing trade requests to arrive outside the previous scheduled cadence.
- Confirm trade execution workflows can accommodate ad hoc rebalancing requests without disrupting other scheduled trading activity.

### For Client Implementation Teams
- During onboarding, configure `AllocationTarget` entries for every asset class or sleeve relevant to each portfolio or model.
- Confirm tolerance bands reflect the client's actual investment policy statement, not a default value carried over from a template.

---

## 6. Testing and Certification

Client implementation teams should complete the following before enabling drift monitoring in a production environment:

- [ ] Configure target allocations and tolerance bands for at least one representative portfolio
- [ ] Simulate a drift event (e.g., via a test price update) and confirm an alert is generated and routed correctly
- [ ] Confirm alert routing respects the `alertRecipients` override where configured
- [ ] Verify batch drift reports and real-time alerts remain consistent with one another for the same portfolio

---

## 7. Rollout Timeline

| Milestone | Date |
|---|---|
| Sandbox availability | [Sample date] |
| Client configuration review window | [Sample date] |
| Production rollout (opt-in) | [Sample date] |

---

## 8. Glossary

- **Target Allocation:** The intended distribution of a portfolio's assets across asset classes, sleeves, or securities, as defined by investment policy.
- **Drift:** The difference between a portfolio's current allocation and its target allocation, typically expressed as a percentage.
- **Tolerance Band:** The amount of drift permitted before rebalancing action is required or an alert is triggered.

---

## 9. Support

For implementation questions related to this sample document, no live support channel applies, as this is a portfolio writing sample rather than a live specification.

---

*This document is an original writing sample. It does not describe or disclose any real product, network, or confidential information.*
