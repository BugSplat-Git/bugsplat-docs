---
description: API Documentation for the BugSplat Charting Endpoints
---

# Charting

Get the number of crashes posted to BugSplat over time. This data can be filtered by application names, application versions, crash group (stack key), start date and end date.

## Crash History

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/crashHistory`

Returns chartable crash data for the specified database, appNames, appVersions, startDate, and endDate.

#### Query Parameters

| Name        | Type   | Description                                                                 |
| ----------- | ------ | --------------------------------------------------------------------------- |
| database    | string | BugSplat database containing crash history                                  |
| appVersions | array  | Comma-separated list of versions to query                                   |
| appNames    | array  | Comma-separated list of applications to query                               |
| startDate   | string | ISO 8601 timestamp representing the start date of the specified time period |
| endDate     | string | ISO 8601 timestamp representing the end date of the specified time period   |

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

| Name           | Type   | Description                                                                 |
| -------------- | ------ | --------------------------------------------------------------------------- |
| database       | string | BugSplat database containing a list of stackKeyIds                          |
| stackKeyIds\[] | array  | Comma-separated list of stackKeyIds to query                                |
| appNames       | array  | Comma-separated list of applications to query                               |
| versions       | array  | Comma-separated list of versions to query                                   |
| stateDate      | date   | ISO 8601 timestamp representing the start date of the specified time period |
| endDate        | date   | ISO 8601 timestamp representing the end date of the specified time period   |

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
