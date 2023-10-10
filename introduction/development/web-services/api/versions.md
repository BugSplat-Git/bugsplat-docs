---
description: API Documentation for the BugSplat Versions Endpoint
---

# Versions

{% hint style="info" %}
This endpoint supports paging, and filtering queries. More information paging filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list stats for crashes that have been posted separated by application and version, create a new version, or set the retired and full dumps flags on a specified version.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/versions" method="get" summary="Versions" %}
{% swagger-description %}
Returns a list of versions in a given database.
{% endswagger-description %}

{% swagger-parameter in="query" name="database" type="string" required="false" %}
BugSplat database containing symbol stores
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
    {
        "Database": "Fred",
        "PageData": null,
        "Rows": [
            {
                "symbolId": "163434",
                "appName": "myConsoleCrasher",
                "version": "2022.4.20.0",
                "size": "14.6255",
                "lastUpdate": "2022-04-20T14:20:14Z",
                "lastReport": "2022-04-20T14:20:14Z",
                "firstReport": "2022-04-20T02:06:31Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163348",
                "appName": "myConsoleCrasher",
                "version": "2022.4.19.0",
                "size": "37.3619",
                "lastUpdate": "2022-04-19T19:55:42Z",
                "lastReport": "2022-04-19T19:55:42Z",
                "firstReport": "2022-04-19T00:52:34Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163338",
                "appName": "myDotNetCrasher",
                "version": "2022.4.19.0",
                "size": "4.1651",
                "lastUpdate": "2022-04-19T18:03:25Z",
                "lastReport": "2022-04-19T18:03:25Z",
                "firstReport": "2022-04-19T00:03:49Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163242",
                "appName": "myConsoleCrasher",
                "version": "2022.4.18.0",
                "size": "49.7142",
                "lastUpdate": "2022-04-18T19:55:30Z",
                "lastReport": "2022-04-18T19:55:30Z",
                "firstReport": "2022-04-18T00:55:05Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163232",
                "appName": "myDotNetCrasher",
                "version": "2022.4.18.0",
                "size": "4.2373",
                "lastUpdate": "2022-04-18T18:03:24Z",
                "lastReport": "2022-04-18T18:03:24Z",
                "firstReport": "2022-04-18T00:02:59Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163162",
                "appName": "myConsoleCrasher",
                "version": "2022.4.17.0",
                "size": "37.3619",
                "lastUpdate": "2022-04-17T19:52:27Z",
                "lastReport": "2022-04-17T19:52:27Z",
                "firstReport": "2022-04-17T00:51:10Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163152",
                "appName": "myDotNetCrasher",
                "version": "2022.4.17.0",
                "size": "4.2329",
                "lastUpdate": "2022-04-17T18:03:28Z",
                "lastReport": "2022-04-17T18:03:28Z",
                "firstReport": "2022-04-17T00:03:07Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163062",
                "appName": "myConsoleCrasher",
                "version": "2022.4.16.0",
                "size": "48.5396",
                "lastUpdate": "2022-04-16T19:57:59Z",
                "lastReport": "2022-04-16T19:57:59Z",
                "firstReport": "2022-04-16T00:52:51Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163052",
                "appName": "myDotNetCrasher",
                "version": "2022.4.16.0",
                "size": "4.3028",
                "lastUpdate": "2022-04-16T18:02:53Z",
                "lastReport": "2022-04-16T18:02:53Z",
                "firstReport": "2022-04-16T00:03:40Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            },
            {
                "symbolId": "163020",
                "appName": "myConsoleCrasher",
                "version": "2022.4.15.0",
                "size": "25.6763",
                "lastUpdate": "2022-04-15T19:51:33Z",
                "lastReport": "2022-04-15T19:51:33Z",
                "firstReport": "2022-04-15T16:29:47Z",
                "reportsPerDay": null,
                "fullDumps": "0",
                "rejectedCount": "0",
                "retired": "0"
            }
        ]
    }
]
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/versions" method="post" summary="Versions" %}
{% swagger-description %}
Used to create a new version and returns a pre-signed URL that can be used to upload new symbol files.
{% endswagger-description %}

{% swagger-parameter in="body" name="symFileName" type="string" required="false" %}
Filename of the symbol file being uploaded to the symbol store via the returned pre-signed URL.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="size" type="number" required="false" %}
Size of symbol file being uploaded to the symbol store via the returned pre-signed URL. Enter 0 to create a placeholder symbol store
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" type="string" required="true" %}
Version of the application symbols being stored in the new symbol store
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" type="string" required="true" %}
Name of application symbols being stored in the new symbol store
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" required="false" %}
BugSplat database in which the symbol store should be created
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "Status": "Success",
    "url": "",
    "database": "Fred",
    "appName": "test",
    "appVersion": "1.0"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/versions" method="put" summary="Versions" %}
{% swagger-description %}
Used to set the retired and fullDumps flags for a specified version.
{% endswagger-description %}

{% swagger-parameter in="body" name="fullDumps" type="0|1" required="false" %}
Flag indicating that the BugSplat Native and .NET SDKs should generate and upload

[full memory dumps](../../../getting-started/integrations/desktop/cplusplus/full-memory-dumps.md)

. This feature incurs additional costs.

[Contact us](../../../../administration/contact-us.md)

to enable full memory dumps for your account.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="retired" type="0|1" required="false" %}
Flag indicating that the version should be marked as retired. Crash reports for retired versions will not be ingested or processed.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" type="string" required="true" %}
Version of application for which the retired/fullDumps flags should be updated
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" type="string" required="true" %}
Name of application for which the retired/fullDumps flags should be updated
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" required="false" %}
BugSplat database in which the symbol store should be created
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "Status": "Success",
    "database": "Fred",
    "symbolId": 163434,
    "retired": 1
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="delete" path="/api/versions" baseUrl="https://app.bugsplat.com" summary="Versions" %}
{% swagger-description %}
Remove symbols from a specified version or versions.
{% endswagger-description %}

{% swagger-parameter in="path" name="database" type="string" required="true" %}
BugSplat database containing versions with symbols for removal
{% endswagger-parameter %}

{% swagger-parameter in="path" name="appName" type="string" %}
Single application name to remove symbols for, required if appVersion is set
{% endswagger-parameter %}

{% swagger-parameter in="path" name="appVersion" type="string" %}
Single application version to remove symbols for, required if appName is set
{% endswagger-parameter %}

{% swagger-parameter in="path" name="appVersions" type="array" %}
Multi-dimensional array of appName and appVersion for symbol removal eg `app0,version0,app1,version1`.  Required if appName and appVersion are not set.
{% endswagger-parameter %}
{% endswagger %}
