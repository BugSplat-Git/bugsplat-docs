---
description: List of Deprecated API endpoints that are scheduled for removal
---

# Deprecated

## Crash History

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/crashHistory`

Returns chartable report ingestion data for the specified database, appNames, appVersions, startDate, and endDate. This endpoint shows report ingestion, rejection, and ignored (retired) stats for subscription purposes. Crashes are logged as they are ingested and may have occurred on different dates (e.g. sent at next launch). For accurate accounting of crashes by date please use the [Crashes](crashes.md) endpoint or [webhook](../../integrating-with-tools/messanger-apps/webhook.md).

{% hint style="info" %}
#### Deprecation Note

This endpoint is deprecated and may be removed in the future. Use the [Dashboard](dashboard.md) endpoint moving forward
{% endhint %}

#### Query Parameters

| Name        | Type   | Description                                                                                              |
| ----------- | ------ | -------------------------------------------------------------------------------------------------------- |
| database    | string | BugSplat database containing crash history                                                               |
| appVersions | array  | Comma-separated list of versions to query                                                                |
| appNames    | array  | Comma-separated list of applications to query                                                            |
| startDate   | string | ISO 8601 timestamp representing the start date of the specified time period                              |
| endDate     | string | ISO 8601 timestamp representing the end date of the specified time period                                |
| timezone    | string | UTC offset (e.g. `+05:30`, `-07:00`). Shifts daily buckets to local day boundaries. Defaults to `+00:00` |

{% tabs %}
{% tab title="200 " %}
```json
{
    "TotalRows": 1,
    "TotalCrashes": 20,
    "StartDay": 738387,
    "EndDay": 738388,
    "Rows": [
        {
            "appName": "All",
            "series": [
                {
                    "day": 738387,
                    "timestamp": 1629417600,
                    "totalCrashCount": 20
                },
                {
                    "day": 738388,
                    "timestamp": 1629504000,
                    "totalCrashCount": 0
                }
            ]
        }
    ]
D}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/crashHistory?database=fred&appNames=MyConsoleCrasher&startDate=2024-12-31T16%3A44%3A50.099Z&endDate=2025-01-07T16%3A48%3A50.099Z' \
--header 'Authorization: Bearer ••••••'
```

## Stack Key Daily Volume

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/stackKeyDailyVolume`

Returns chartable volumes for a given database and a comma-separated list of stackKeyIds.

#### Query Parameters

| Name           | Type   | Description                                                                                                          |
| -------------- | ------ | -------------------------------------------------------------------------------------------------------------------- |
| database       | string | BugSplat database containing a list of stackKeyIds                                                                   |
| stackKeyIds\[] | array  | Comma-separated list of stackKeyIds to query                                                                         |
| appNames       | array  | Comma-separated list of applications to query                                                                        |
| versions       | array  | Comma-separated list of versions to query                                                                            |
| startDate      | date   | ISO 8601 timestamp representing the start date of the specified time period                                          |
| endDate        | date   | ISO 8601 timestamp representing the end date of the specified time period                                            |
| timezone       | string | UTC offset (e.g. `+05:30`, `-07:00`). Shifts daily and hourly buckets to local time boundaries. Defaults to `+00:00` |

{% tabs %}
{% tab title="200 " %}
```json
[
    [
        "Date",
        "myConsoleCrasher!MemoryException(150)"
    ],
    [
        "2021-07-26",
        14
    ]
]
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location --globoff 'https://app.bugsplat.com/api/stackKeyDailyVolume?database=fred&appNames=MyConsoleCrasher&stackKeyIds[]=10315&versions=2025.1.7.0&startDate=2024-12-31T16%3A44%3A50.099Z&endDate=2025-01-07T16%3A48%3A50.099Z' \
--header 'Authorization: ••••••'
```
