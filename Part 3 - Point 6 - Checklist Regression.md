# Checklist for Regression Impact: Route Reassignment

This checklist highlights the core modules and operational flows within the Oppizi platform that could be indirectly impacted by the new route reassignment constraints and 24-hour lock mechanism.

## 1. Upstream Campaign Management
*   [ ] **Campaign State Transitions:** Ensure moving a route doesn't prematurely alter campaign statuses (e.g., forcing a `Draft` to `Active` or throwing validation faults on initialization).
*   [ ] **Inventory & QR Pool Allocations:** Verify that flyer stock levels and unique QR code batches stay safely bound to the root campaign when a route switches hands, preventing inventory count leaks or double-allocation.
*   [ ] **Bulk Import Tools:** Validate that CSV/bulk dashboard upload tools strictly respect the 24-hour edit lock and gracefully reject alterations to frozen schedules instead of overwriting records.

## 2. Field App Shifts & Operations
*   [ ] **Mobile Cache Synchronization:** Verify that the original agent's app immediately clears the reassigned route and the receiving agent's app updates instantly without requiring a hard manual logout.
*   [ ] **Check-In/Check-Out Actions:** Confirm that old agents are strictly blocked from starting shifts on routes they no longer own, protecting the integrity of real-time field activity tracking.

## 3. Data, Analytics & Security
*   [ ] **Reporting Engine Aggregations:** Ensure all field delivery statistics, scanned flyer counts, and time logs seamlessly shift attribution to the receiving agent without corrupting historical database snapshots.
*   [ ] **Geofencing & Audit Streams:** Verify that automated compliance modules track the new agent's coordinates against the route path, preventing false-positive "out-of-bounds" warnings.
*   [ ] **Role-Based API Locks:** Confirm that the 24-hour edit lock is enforced consistently across all API entry points, stopping unauthorized dashboard profiles from bypassing the restriction via raw HTTP requests.
