---
description: API Documentation for various endpoints related to BugSplat Crash Groups
---

# Crash Groups

{% hint style="info" %}
These endpoints support paging and filtering queries. More information paging and filtering is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a summary of all [crash groups](../../../../education/bugsplat-terminology.md#crash-group), get a list of crashes in a particular group, or detailed information on individual crash groups that share a common function name and line number at the top of the call stack and have been grouped at a lower level of the call stack.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/summary" method="get" summary="Summary" %}
{% swagger-description %}
Returns crash summary data. Supports paging and filtering using column names startDate, endDate, stackKey, stackKeyId, crashSum, userSum, subKeyDepth, defectId, comments, subject.
{% endswagger-description %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing summary data
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
    {
        "Database": "tormore",
        "PageData": null,
        "Rows": [
            {
                "stackKey": "myConsoleCrasher+0x12b5",
                "stackKeyId": "208",
                "lastReport": "2021-08-24T17:59:30Z",
                "firstReport": "2021-07-29T12:06:05Z",
                "crashSum": "178",
                "userSum": "7",
                "techSupportSubject": null,
                "defectId": null,
                "stackKeyDefectUrl": null,
                "stackKeyDefectLabel": null,
                "comments": "Dave left a comment",
                "subKeyDepth": "1"
            }
        ]
    }
]
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/keycrash" method="get" summary="Key Crash" %}
{% swagger-description %}
Get a list of crashes for a particular crash group (aka Stack Key). This query supports paging, filtering, and grouping. More information on how to use paging, filtering, and grouping can be found here. All of the property keys in the Rows object can be used as column values for filtering and grouping e.g. id, email, IpAddress etc.
{% endswagger-description %}

{% swagger-parameter in="query" name="stackKeyId" type="number" %}
ID for the desired crash group
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing crash data
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
  {
    "Database": "Fred",
    "PageData": {
      "stackKeyId": 5555,
      "stackKey": "myConsoleCrasher!MemoryException(150)",
      "isSubBucket": false,
      "isSubKeyedStack": false,
      "defectTracker": true,
      "defectTrackerType": "Jira",
      "stackKeyDefectId": 0,
      "stackKeyComments": "This is a critical defect - needs to be fixed for our upcoming version release",
      "firstCrashTime": "2019-06-20T19:11:32Z",
      "lastCrashTime": "2021-08-19T10:42:33Z",
      "events": [
        {
          "id": "71",
          "type": "Comment",
          "timestamp": "2021-06-23T14:30:07Z",
          "uId": "27578",
          "username": "fred@bugsplat.com",
          "firstName": "Fred",
          "lastName": "Flintstone",
          "message": "# Here is markup\r\n* one\\ud83d\\ude0e\r\n* two\r\n* three "
        },
      ]
    },
    "Rows": [
      {
        "id": "103146",
        "appName": "myConsoleCrasher",
        "appVersion": "2021.8.19.0",
        "appDescription": "",
        "crashTime": "2021-08-19T10:42:33Z",
        "user": "f2cb32166f77f33e80311be40c97466e",
        "email": "d000fa8829a03739863dbe3379e1568f",
        "userDescription": "This is the default user crash description.",
        "IpAddress": "54.144.81.xxxx",
        "defectId": null,
        "defectUrl": "",
        "defectLabel": "",
        "skDefectUrl": "",
        "skDefectLabel": "",
        "Comments": null,
        "crashTypeId": "1",
        "exceptionCode": "c0000005",
        "exceptionMessage": "Access violation",
        "groupByCount": null
      }
    ]
  }
]
```
{% endswagger-response %}
{% endswagger %}
