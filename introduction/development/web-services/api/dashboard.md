---
description: API Documentation for the BugSplat Dashboard Endpoint
---

# Dashboard

Get a consolidated summary of report data for the dashboard page. This endpoint returns report volume, report history, recent crashes, status counts, and top report groups a single request.

## Dashboard Summary

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/dashboard`

Returns a consolidated dashboard summary for the specified database and time range, including report volume, report history chart data, recent reports, status counts, and top report groups. Reports are counted as they are ingested and may have occurred on different dates (e.g. sent at next launch). For accurate accounting of reports by date please use the [Crashes](crashes.md) endpoint or [webhook](../../integrating-with-tools/messanger-apps/webhook.md).

#### Query Parameters

| Name        | Type   | Description                                                            |
| ----------- | ------ | ---------------------------------------------------------------------- |
| database    | string | **Required.** BugSplat database name                                   |
| startDate   | string | ISO 8601 timestamp for the start of the time range. If omitted, defaults to 365 days before `endDate`. |
| endDate     | string | ISO 8601 timestamp for the end of the time range. If omitted, defaults to the current time.   |
| appNames    | string | Comma-separated list of application names to filter by                 |
| appVersions | string | Comma-separated list of application versions to filter by              |
| timezone    | string | UTC offset (e.g. `+05:30`). Defaults to `+00:00`                       |
| interval    | string | `hourly` or `daily`. Defaults to `daily`                               |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "database": "Fred",
    "volume30Day": 1234,
    "uploaded30Day": 1100,
    "crashDataDays": 45,
    "crashHistory": {
        "totalRows": 1,
        "totalCrashes": 500,
        "totalUploaded": 480,
        "rows": [
            {
                "appName": "All",
                "series": [
                    {
                        "day": 738387,
                        "timestamp": 1629417600,
                        "totalCrashCount": 20,
                        "uploadedCount": 18
                    }
                ]
            }
        ]
    },
    "lastCrashTime": "2026-02-20T12:05:00Z",
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
    ],
    "totalThrottled": 3,
    "totalThrottledPrevious": 2
}
```
{% endtab %}
{% endtabs %}

### Response Fields

| Field         | Type   | Description                                                                                   |
| ------------- | ------ | --------------------------------------------------------------------------------------------- |
| status        | string | `"success"` on success                                                                        |
| database      | string | The database name                                                                             |
| volume30Day   | number | Total crash count in the last 30 days, counted when reports are accepted for upload. Includes reports that never finished uploading |
| uploaded30Day | number | Number of crash reports that completed upload and were stored in the last 30 days. May be lower than `volume30Day` when clients abandon uploads |
| crashDataDays | number | Number of days of crash data available                                                        |
| crashHistory  | object | Chart data for crash volume over time. Contains `totalRows`, `totalCrashes`, `totalUploaded`, and `rows` array. Each series entry contains `totalCrashCount` and `uploadedCount` |
| lastCrashTime | string | ISO 8601 timestamp of the most recent crash overall, not limited by the requested time range or filters. `null` if no crashes exist |
| recentCrashes | array  | The 5 most recent crashes in the time range                                                   |
| statusCounts  | object | Crash group status breakdown for `current` and `previous` time periods                        |
| topStackKeys  | array  | Top 6 stack keys by crash count in the time range                                             |
| totalThrottled | number | Total number of throttled crashes in the time range                                          |
| totalThrottledPrevious | number | Total number of throttled crashes in the previous time period                        |

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred&startDate=2025-01-01T00%3A00%3A00.000Z&endDate=2026-02-23T00%3A00%3A00.000Z' \
--header 'Authorization: Bearer ••••••'
```

### Curl Example (Default Date Range)

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred' \
--header 'Authorization: Bearer ••••••'
```

Omitting `startDate` and `endDate` does not return all-time data; it defaults to the last 365 days.

### Curl Example (with filters)

```bash
curl --location 'https://app.bugsplat.com/api/dashboard?database=fred&startDate=2025-01-01T00%3A00%3A00.000Z&endDate=2026-02-23T00%3A00%3A00.000Z&appNames=MyConsoleCrasher&appVersions=1.0&interval=hourly' \
--header 'Authorization: Bearer ••••••'
```
