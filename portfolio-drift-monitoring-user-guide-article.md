# Portfolio Drift Monitoring

*Writing sample: User guide*

## Overview

**Portfolio Drift Monitoring** shows how far a portfolio's current asset allocation has moved from its target allocation. It alerts you when a position or asset class reaches or exceeds its tolerance limit.

Use Drift Monitoring to identify allocation changes between scheduled portfolio reviews.

This article explains how to access Drift Monitoring, interpret drift information, and respond to drift alerts.

## Open Drift Monitoring

1. Go to **Portfolio Management** > **Drift Monitoring**.
2. If **Drift Monitoring** isn't available, ask your administrator to enable **Drift Monitoring (View/Edit)** under **Settings** > **Role Permissions**.

## View portfolio drift

When you open Drift Monitoring, the table lists the portfolios that you have permission to view.

| Column | Description |
|---|---|
| **Portfolio** | The name of the portfolio or model. |
| **Asset class** | The asset class or sleeve being monitored. |
| **Target weight** | The target allocation for the asset class, as defined in the investment policy. |
| **Current weight** | The current allocation for the asset class. This value is updated as prices and positions change. |
| **Drift status** | The current status: **Within tolerance**, **Approaching limit**, or **Breached**. |

By default, portfolios with a **Breached** status appear at the top of the list and are highlighted.

### Interpret the drift indicator

Each row includes a bar that shows the current weight relative to the target weight and tolerance band.

- The shaded center section represents the allowed range.
- The marker represents the portfolio's current weight.
- A marker outside the shaded section indicates that the portfolio has breached its tolerance band for that asset class.

## Respond to a drift alert

1. Select a **Breached** or **Approaching limit** row to open the detail panel.
2. Review the **Drift history** chart. The chart shows how the allocation has changed over the past 30 days.
3. Select **Create rebalancing trade** to open a pre-filled trade ticket. The ticket is sized to bring the position back within its target range.
4. If no trade is needed, select **Acknowledge**.
5. If you acknowledge a breach without creating a trade, enter a brief note explaining why. The note is added to the portfolio's audit history.

## Set up drift alerts

By default, drift alerts appear only in Drift Monitoring. You can also receive an email or in-app notification when a portfolio breaches its tolerance band.

1. Go to **Settings** > **Notifications** > **Drift Alerts**.
2. Select the portfolios or portfolio groups that you want to monitor.
3. Select a notification method:
   - **Email**
   - **In-app**
   - **Both**
4. Select **Save**.

## Tips for monitoring drift

- Review the **Drift history** chart before taking action. A position that briefly reaches its tolerance limit and returns to the allowed range might not require a trade. A position that moves steadily toward or beyond the limit might require further review.
- If you manage multiple portfolios that use the same model, check **Drift Monitoring** at the model level first. Similar drift across portfolios can indicate a market move affecting the asset class rather than an issue with an individual portfolio.
- Acknowledging a breach doesn't remove it from the list. The breach remains visible with an **Acknowledged** tag until the position returns within its tolerance band.

## Related topics

- Configure target allocations and tolerance bands
- Set up role permissions for Drift Monitoring
- Understand drift status definitions

---

*This document is an original writing sample. It doesn't describe or disclose any real product, network, or confidential information.*
