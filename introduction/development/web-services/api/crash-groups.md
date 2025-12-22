---
description: API Documentation for various endpoints related to BugSplat Crash Groups
---

# Crash Groups

{% hint style="info" %}
These endpoints support paging, filtering, and grouping queries. More information on paging, filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a summary of all [crash groups](../../../../education/bugsplat-terminology.md#crash-group), get a list of crashes in a particular group, or detailed information on individual crash groups that share a common function name and line number at the top of the call stack and have been grouped at a lower level of the call stack.

## Get Summary

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/v2/summary`

Returns crash summary data. Supports paging and filtering using column names firstReport, lastReport, stackKey, stackKeyId, crashSum, userSum, subKeyDepth, defectId, comments, and techSupportSubject.

#### Query Parameters

| Name     | Type   | Description                               |
| -------- | ------ | ----------------------------------------- |
| database | string | BugSplat database containing summary data |

{% tabs %}
{% tab title="200 " %}
```json
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
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/v2/summary' \
--header 'Authorization: Bearer ••••••'
```

## Get Key Crash

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/v2/keycrash`

Obtain a list of crashes for a specific crash group (also known as Stack Key). This query supports paging, filtering, and grouping. For more information on paging, filtering, and grouping, please refer to this [link](../paging-filtering-and-grouping.md). All the property keys in the Rows object can be used as column values for filtering and grouping, such as id, email, and IP address.

#### Query Parameters

| Name       | Type   | Description                             |
| ---------- | ------ | --------------------------------------- |
| database   | string | BugSplat database containing crash data |
| stackKeyId | number | ID for the desired crash group          |

{% tabs %}
{% tab title="200 " %}
```json
{
  "database": "Fred",
  "pageData": {
    "stackKeyId": 10999,
    "stackKey": "MyConsoleCrasher!MemoryException(238)",
    "defectTracker": true,
    "defectTrackerType": "GitHub",
    "stackKeyDefectId": 0,
    "stackKeyComments": null,
    "stackKeyDefectUrl": "",
    "stackKeyDefectLabel": "",
    "firstCrashTime": "2025-10-24T00:58:00Z",
    "lastCrashTime": "2025-10-24T01:35:00Z",
    "events": []
  },
  "rows": [
    {
      "status": "0",
      "id": "140577",
      "stackId": "26497",
      "stackKey": "MyConsoleCrasher!MemoryException(238)",
      "stackKeyId": "10999",
      "appName": "MyConsoleCrasher",
      "appVersion": "1.0.0",
      "appDescription": "",
      "crashTime": "2025-10-24T01:35:47Z",
      "user": "Fred",
      "email": "fred@bugsplat.com",
      "userDescription": "This is the default user crash description.",
      "IpAddress": "71.117.187.xxxx",
      "defectId": null,
      "defectUrl": "",
      "defectLabel": "",
      "skDefectUrl": "",
      "skDefectLabel": "",
      "Comments": "Additional 'notes' data supplied through API",
      "crashTypeId": "1",
      "exceptionCode": "c0000005",
      "exceptionMessage": "Access violation",
      "attributes": "{}",
      "groupByCount": null
    }
  ]
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/keycrash?database=fred&stackKeyId=10315' \
--header 'Authorization: Bearer ••••••'
```
