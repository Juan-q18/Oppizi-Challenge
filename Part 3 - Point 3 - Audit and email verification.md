## 3. Audit and Email Verification Steps

### System Audit Log Verification

#### Execution Steps:
1. Open your API testing tool (e.g., Postman) or launch the browser's **Network Developer panel** (`F12` -> `Network` tab).
2. Complete a successful route reassignment on the dashboard UI for **Route R-101**.
3. Send a direct query to the audit service API endpoint:
   * **Method:** `GET`
   * **URL:** `{{baseUrl}}/api/v1/audit-logs?resourceId=R-101&limit=1`
   * **Headers:** `Authorization: Bearer <manager_token>`

#### Validation Criteria:
* [ ] **HTTP Status Code:** Confirm the server returns an exact `200 OK` response.
* [ ] **Payload Structure:** Validate that the returned JSON payload perfectly matches the schema below:

```json
{
  "status": "success",
  "data": {
    "event": "route.reassigned",
    "timestamp": "2026-06-02T22:58:10Z",
    "actor": {
      "id": "mgr_842",
      "role": "Campaign Manager"
    },
    "resource": {
      "type": "route",
      "id": "R-101"
    },
    "change_details": {
      "previous_agent_id": "agnt_001_john",
      "new_agent_id": "agnt_005_sarah",
      "lock_applied": true,
      "lock_expires_at": "2026-06-03T22:58:10Z"
    }
  }
}