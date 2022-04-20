---
description: API Documentation for the BugSplat Charting Endpoints
---

# Charting

Get the number of crashes posted to BugSplat over time. This data can be filtered by application names, application versions, crash group (stack key), start date and end date.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/crashHistory" method="get" summary="Crash History" %}
{% swagger-description %}
Returns chartable crash data for the specified database, appNames, appVersions, startDate, and endDate.
{% endswagger-description %}

{% swagger-parameter in="query" name="endDate" type="string" %}
ISO 8601 timestamp representing the end date of the specified time period
{% endswagger-parameter %}

{% swagger-parameter in="query" name="startDate" type="string" %}
ISO 8601 timestamp representing the start date of the specified time period
{% endswagger-parameter %}

{% swagger-parameter in="query" name="appVersions" type="array" %}
Comma-separated list of versions to query
{% endswagger-parameter %}

{% swagger-parameter in="query" name="appNames" type="array" %}
Comma-separated list of applications to query
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing crash history
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
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
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/stackKeyDailyVolume" method="get" summary="Stack Key Daily Volume" %}
{% swagger-description %}
Returns chartable volumes for a given database and a comma-separated list of stackKeyIds.
{% endswagger-description %}

{% swagger-parameter in="query" name="stackKeyIds" type="array" %}
Comma-separated list of stackKeyIds to query
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing a list of stackKeyIds
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
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
{% endswagger-response %}
{% endswagger %}
