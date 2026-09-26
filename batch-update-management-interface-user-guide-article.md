# What's new: Batch Update Management Interface

## Overview

The Batch Update Management interface gives you a single screen where you can review, filter, and act on pending updates to your scheduled batch jobs, without opening each job individually. Use it to adjust run times ahead of a holiday, pause a group of jobs during maintenance, or check which jobs changed recently.

This article describes what's new, where to find the interface, and how to use its main features.

---

## Why we added this feature

Previously, to review or update multiple batch jobs, you had to open each job's settings one at a time. This approach works for a single job, but it's slow when you need to, for example, pause 15 jobs before a maintenance window or check which jobs changed in the last week. Batch Update Management brings these multi-job tasks into a single screen with filtering, sorting, and bulk actions.

---

## Where to find it

Go to **Batch Manager** > **Update Management** in the navigation menu. If you don't see this option, ask your administrator to enable it for your role under **Settings** > **Role Permissions** > **Batch Update Management (View/Edit)**.

---

## Use the interface

### View your jobs

When you open Update Management, you see a table that lists every batch job you have permission to view.

| Column | Description |
|---|---|
| **Job name** | The name of the scheduled batch job. |
| **Status** | The job's current status: Scheduled, Running, Held, Failed, or Disabled. |
| **Last modified** | The date, time, and user who last changed the job's configuration. |
| **Next run** | The job's next scheduled run time, based on its current configuration. |
| **Dependencies** | Indicates whether the job has predecessor jobs configured. |

### Filter and sort the list

Use the filter bar above the table to narrow the list by **Status**, **Last modified** date range, or **Job group**. Select any column header to sort the list. For example, sort by **Last modified** to see everything that changed since your last review.

### Select and update multiple jobs

1. Select the checkbox next to each job that you want to update, or select the checkbox in the table header to select every job in the current filtered view.
2. Select **Bulk actions** in the toolbar above the table.
3. Select an action: **Pause selected**, **Resume selected**, **Reschedule selected**, or **Reassign owner**.
4. If you select **Reschedule selected**, enter a new run time or an offset (for example, "delay by 2 hours") to apply to every selected job.
5. Review the confirmation summary, which lists the jobs the action affects, and then select **Apply**.

### Review recent changes

Select the **Change history** tab at the top of the screen to see a chronological list of updates made through this interface, including who made each change and what they changed. Use this tab to confirm that a bulk update applied correctly or to investigate an unexpected job status.

---

## Tips

- Before a scheduled review meeting, use the **Last modified** filter to quickly find everything that changed since the last one.
- When you use **Reschedule selected** with an offset (for example, "+2 hours") instead of a fixed time, each job shifts relative to its own original schedule. This option is useful when the jobs in the group don't all run at the same time.
- Bulk actions respect existing job dependencies. Pausing a predecessor job doesn't automatically pause its dependents, so review the **Dependencies** column before you assume that a bulk pause covers an entire chain.

---

## Related topics

- Configure job dependencies
- Set up role permissions for Batch Manager
- Understand job status definitions

---

*This document is an original writing sample. It does not describe or disclose any real product, network, or confidential information.*
