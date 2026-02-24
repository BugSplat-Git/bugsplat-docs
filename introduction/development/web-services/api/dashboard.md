---
description: API Documentation for the BugSplat Dashboard Endpoint
---

# Dashboard

Get a consolidated summary of crash data for the dashboard page. This endpoint returns crash volume, crash history, recent crashes, status counts, and top stack keys in a single request.

## Dashboard Summary

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/dashboard`

Returns a consolidated dashboard summary for the specified database and time range, including crash volume, crash history chart data, recent crashes, status counts, and top stack keys.

#### Query Parameters

| Name        | Type   | Description                                                                 |
| ----------- | ------ | --------------------------------------------------------------------------- |
| database    | string | **Required.** BugSplat database name                                        |
| startDate   | string | ISO 8601 timestamp for the start of the time range. Omit for All Time.     |
| endDate     | string | ISO 8601 timestamp for the end of the time range. Omit for All Time.       |
| appNames    | string | Comma-separated list of application names to filter by                      |
| appVersions | string | Comma-separated list of application versions to filter by                   |
| timezone    | string | UTC offset (e.g. `+05:30`). Defaults to `+00:00`                           |
| interval    | string | `hourly` or `daily`. Defaults to `daily`                                    |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "database": "Fred",
    "volume30Day": 1234,
    "crashDataDays": 45,
    "crashHistory": {
        "totalRows": 1,
        "totalCrashes": 500,
        "rows": [
            {
                "appName": "All",
                "series": [
                    {
                        "day": 738387,
                        "timestamp": 1629417600,
                        "totalCrashCount": 20
                    }
                ]
            }
        ]
    },
    "recentCrashes": [
        {
            "id": 1,
            "status": 0,
            "stackKey": "crash_in_main",
            "stackKeyId": 42,
            "crashTime": "2026-02-20T12:00:00Z",
            "appName": "MyApp",
            "appVersion": "1.0",
            "exceptionMessage": "Access violation"
        }
    ],
    "statusCounts": {
        "current": {
            "open": 10,
            "closed": 5,
            "regression": 2,
            "ignored": 1,
            "total": 18
        },
        "previous": {
            "open": 8,
            "closed": 6,
            "regression": 1,
            "ignored": 2,
            "total": 17
        }
    },
    "topStackKeys": [
        {
            "stackKey": "crash_in_main",
            "stackKeyId": 42,
            "crashSum": 150
        }
    ]
}
```
{% endtab %}
{% endtabs %}

### Response Fields

| Field          | Type   | Description                                                                                      |
| -------------- | ------ | ------------------------------------------------------------------------------------------------ |
| status         | string | `"success"` on success                                                                           |
| database       | string | The database name                                                                                |
| volume30Day    | number | Total crash count in the last 30 days                                                            |
| crashDataDays  | number | Number of days of crash data available                                                           |
| crashHistory   | object | Chart data for crash volume over time. Contains `totalRows`, `totalCrashes`, and `rows` array    |
| recentCrashes  | array  | The 5 most recent crashes in the time range                                                      |
| statusCounts   | object | Crash group status breakdown for `current` and `previous` time periods                           |
| topStackKeys   | array  | Top 6 stack keys by crash count in the time range                                                |

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred&startDate=2025-01-01T00%3A00%3A00.000Z&endDate=2026-02-23T00%3A00%3A00.000Z' \
--header 'Authorization: Bearer ••••••'
```

### Curl Example (All Time)

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred' \
--header 'Authorization: Bearer ••••••'
```

### Curl Example (with filters)

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred&startDate=2025-01-01T00%3A00%3A00.000Z&endDate=2026-02-23T00%3A00%3A00.000Z&appNames=MyConsoleCrasher&appVersions=1.0&interval=hourly' \
--header 'Authorization: Bearer ••••••'
```
