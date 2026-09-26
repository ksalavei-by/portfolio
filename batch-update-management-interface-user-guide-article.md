# Batch Update Management Interface

## Overview

The **Batch Update Management** interface provides a single screen for reviewing, filtering, and updating scheduled batch jobs. You can use it to change run times, pause jobs during maintenance, or review recent changes without opening each job individually.

This article explains how to open Batch Update Management and use its main features.

## What you can do

With Batch Update Management, you can:

- View multiple batch jobs in one table.
- Filter and sort jobs by status, modification date, or job group.
- Apply an action to multiple jobs at the same time.
- Review changes made through the interface.

## Open Batch Update Management

1. Go to **Batch Manager** > **Update Management**.
2. If **Update Management** isn't available, ask your administrator to enable **Batch Update Management (View/Edit)** under **Settings** > **Role Permissions**.

## View your jobs

When you open Update Management, the table lists the batch jobs that you have permission to view.

| Column | Description |
|---|---|
| **Job name** | The name of the scheduled batch job. |
| **Status** | The current job status: **Scheduled**, **Running**, **Held**, **Failed**, or **Disabled**. |
| **Last modified** | The date, time, and user associated with the most recent configuration change. |
| **Next run** | The next scheduled run time based on the current configuration. |
| **Dependencies** | Indicates whether predecessor jobs are configured for the job. |

## Filter and sort jobs

Use the filter bar above the table to filter jobs by:

- **Status**
- **Last modified** date range
- **Job group**

Select a column heading to sort the list. For example, sort by **Last modified** to find jobs that changed since your previous review.

## Update multiple jobs

1. Select the checkbox next to each job that you want to update. To select all jobs in the current filtered view, select the checkbox in the table header.
2. Select **Bulk actions** in the toolbar.
3. Select an action:
   - **Pause selected**
   - **Resume selected**
   - **Reschedule selected**
   - **Reassign owner**
4. If you selected **Reschedule selected**, enter a new run time or an offset, such as `2 hours`, to apply to all selected jobs.
5. Review the confirmation summary.
6. Select **Apply**.

The confirmation summary lists the jobs affected by the action.

## Review recent changes

Select the **Change history** tab to view changes made through Batch Update Management.

The change history lists updates in chronological order and includes who made each change and what they changed. Use this information to verify that a bulk update was applied correctly or to investigate an unexpected job status.

## Tips

- Before a scheduled review, use the **Last modified** filter to find jobs modified since the previous review.
- When you use **Reschedule selected** with an offset, such as `+2 hours`, the offset is applied to each job's existing schedule. This option is useful when selected jobs have different run times.
- Bulk actions respect existing job dependencies. Pausing a predecessor job doesn't automatically pause its dependent jobs. Review the **Dependencies** column before pausing jobs in a dependency chain.

## Related topics

- Configure job dependencies
- Set up role permissions for Batch Manager
- Understand job status definitions

---

*This document is an original writing sample. It doesn't describe or disclose any real product, network, or confidential information.*
