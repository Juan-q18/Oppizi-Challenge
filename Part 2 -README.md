# Open Charge Map API Tests

Postman collection for testing the [Open Charge Map API](https://map.openchargemap.io/).

## Endpoints Covered

| Endpoint | Method | Description |
|---|---|---|
| `/poi/` | GET | Retrieves charging station POIs |
| `/referencedata/` | GET | Returns static reference data (charger types, countries, etc.) |

## Setup Instructions

### Prerequisites

- [Postman](https://www.postman.com/downloads/) (desktop app)

### Import the Collection

1. Open Postman
2. Go to **File > Import**
3. Select the file `Open Charge Map API Tests.postman_collection.json`
4. Click **Import**

### Configure Variables

Set these collection variables (right-click collection > **Edit** > **Variables** tab):

| Variable | Value |
|---|---|
| `baseUrl` | `https://api.openchargemap.io/v3` |
| `apiKey` | Your API key (obtain from [Open Charge Map](https://map.openchargemap.io/)) |

To get an API key:
1. Open https://map.openchargemap.io/
2. Open browser DevTools (F12) > Network tab
3. Interact with the map and inspect any API call to `openchargemap.io`
4. Copy the `key` query parameter value

### Run Tests

- Run the entire collection: click **Run** on the collection → **Run Open Charge Map API Tests**
- Run individual requests: open the request → click **Send**

## Test Report

### Summary

| Endpoint | Status | Response Time |
|---|---|---|
| `GET /poi/` | Passed | < 1000ms |
| `GET /referencedata/` | Passed | < 1000ms |

### Test Details

#### `GET /poi/`

| Test | Description | Result |
|---|---|---|
| Status code is 200 OK | Validates HTTP 200 | Passed |
| Response time is under 1000ms | Validates performance | Passed |
| Response is a valid array and respects maxresults | Checks JSON schema (array, ID, AddressInfo, Lat/Lng) and business logic (≤3 results) | Passed |

**Sample request:**

```
GET {{baseUrl}}/poi/?key={{apiKey}}&latitude=-34.6037&longitude=-58.3816&distance=10&countrycode=AR&maxresults=3
```

#### `GET /referencedata/`

| Test | Description | Result |
|---|---|---|
| Status code is 200 OK | Validates HTTP 200 | Passed |
| Response time is under 1000ms | Validates performance | Passed |
| Response is a valid reference data object | Validates schema (ChargerTypes, ConnectionTypes, Countries, StatusTypes arrays with ID/Title) | Passed |

**Sample request:**

```
GET {{baseUrl}}/referencedata/?key={{apiKey}}
```

### Observations

- The `/referencedata/` endpoint accepts the same query parameters as `/poi/`, but they are ignored since it returns static reference data regardless of location.
- The `/poi/` endpoint correctly limits results to the value of `maxresults`.
- Both endpoints return responses well under the 1000ms threshold.
- The collection includes **Postman Visualizer** templates for rich HTML rendering of results.
