---
description: API Documentation for the BugSplat Crashes Endpoint
---

# Crashes

{% hint style="info" %}
This endpoint supports paging, filtering, and grouping queries. More information paging filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list of crashes that have been posted to BugSplat

## Crashes

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/allcrash`

Get a list of crashes for a database. This query supports paging, filtering, and grouping. More information on how to use paging, filtering, and grouping can be found here. All of the property keys in the Rows object can be used as column values for filtering and grouping e.g. id, stackKey, appName, ipAddress, etc.

#### Query Parameters

| Name     | Type   | Description                             |
| -------- | ------ | --------------------------------------- |
| database | string | BugSplat database containing crash data |

{% tabs %}
{% tab title="200 " %}
```
[
  {
    "Database": "Fred",
    "PageData": null,
    "Rows": [
      {
        "id": "103146",
        "stackKey": "myConsoleCrasher!MemoryException(150)",
        "stackKeyId": "5555",
        "appName": "myConsoleCrasher",
        "appVersion": "2021.8.19.0",
        "appDescription": "",
        "userDescription": "This is the default user crash description.",
        "user": "f2cb32166f77f33e80311be40c97466e",
        "email": "d000fa8829a03739863dbe3379e1568f",
        "IpAddress": "54.144.81.xxxx",
        "crashTime": "2021-08-19T10:42:33Z",
        "defectId": null,
        "defectUrl": "",
        "defectLabel": "",
        "skDefectId": null,
        "skDefectUrl": "",
        "skDefectLabel": "",
        "Comments": null,
        "skComments": "This is a critical defect - needs to be fixed for our upcoming version release",
        "crashTypeId": "1",
        "exceptionCode": "c0000005",
        "exceptionMessage": "Access violation",
        "lineNumber": null,
        "groupByCount": null
      }
    ]
  }
]
```
{% endtab %}
{% endtabs %}
