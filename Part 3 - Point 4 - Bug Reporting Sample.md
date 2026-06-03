# Bug Report: System Allows Immediate Secondary Reassignment of Route Within 24-Hour Lock Period

## Bug ID
BUG-2026-1024

## Summary
The system fails to enforce the mandatory 24-hour lock period following a route reassignment. A Campaign Manager is able to perform an immediate secondary reassignment of the same route to another agent within minutes of the first change, directly violating the business specification.

## Prerequisites
1. A route (e.g., `R-101`, Outdoor) exists in an upcoming campaign starting in 5 days.
2. At least three agents exist in the system matching the route criteria:
   * `Agent John` (Initially assigned)
   * `Agent Sarah` (Eligible for reassignment)
   * `Agent Alex` (Eligible for secondary reassignment)

## Steps to Reproduce
1. Log into the Admin Dashboard as a **Campaign Manager**.
2. Navigate to **Campaigns > Route Schedule** and locate route `R-101` (currently assigned to `Agent John`).
3. Click **Reassign**, select `Agent Sarah`, and confirm the change. 
   * *System displays success message and locks the route UI contextually.*
4. Refresh the page or navigate away and return immediately to route `R-101`.
5. Click **Reassign** again on route `R-101`.
6. Select `Agent Alex` and click **Confirm Reassignment**.

## Expected Result
* The system should **deny** the second reassignment attempt at Step 6.
* An error message should be displayed: `"This route is currently locked for adjustments"`.
* The route assignment should remain with `Agent Sarah`.

## Actual Result
* The system **successfully processes** the secondary reassignment to `Agent Alex` after only 10 minutes.
* No error message or validation block is triggered.
* The route assignment changes to `Agent Alex`, bypassing the 24-hour lock rule entirely.

## Audit & Logs Trace
* `route.reassigned` event was generated twice in rapid succession.
* **Log Entry #1:** `previous_agent: Agent John`, `new_agent: Agent Sarah` (Timestamp: `2026-06-02 23:30:00`)
* **Log Entry #2:** `previous_agent: Agent Sarah`, `new_agent: Agent Alex` (Timestamp: `2026-06-02 23:40:00`)

## Attachments / Evidence
* `console_network_trace.har` (Shows `POST /api/v1/routes/R-101/reassign` returning `200 OK` instead of `423 Locked`)
* `reassignment_bypass_screen_recording.mp4`
