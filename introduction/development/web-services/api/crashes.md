---
description: API Documentation for the BugSplat Crashes Endpoint
---

# Crashes

{% hint style="info" %}
This endpoint supports paging, filtering, and grouping queries. More information paging, filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list of crashes that have been posted to BugSplat

## Crashes

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/crashes`

Get a list of crashes for a database. This query supports paging, filtering, and grouping.  All of the property keys in the Rows object can be used as column values for filtering and grouping e.g. id, stackKey, appName, ipAddress, etc. &#x20;

#### Query Parameters

| Name     | Type   | Description                             |
| -------- | ------ | --------------------------------------- |
| database | string | BugSplat database containing crash data |

{% tabs %}
{% tab title="200 " %}
```json
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

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/crashes' \
--header 'Authorization: Bearer ••••••' \
--form 'database="fred"' \
--form 'filtergroupopen0="1"' \
--form 'filteroperator0="0"' \
--form 'filterdatafield0="appName"' \
--form 'filtercondition0="EQUAL"' \
--form 'filtervalue0="MyConsoleCrasher"' \
--form 'filtergroupclose0="1"' \
--form 'filtergroupopen1="1"' \
--form 'filteroperator1="0"' \
--form 'filterdatafield1="crashTime"' \
--form 'filtercondition1="GREATER_THAN"' \
--form 'filtervalue1="2024-12-08T15:59:56.627Z"' \
--form 'filtergroupclose1="1"' \
--form 'filterscount="2"' \
--form 'groupscount="0"' \
--form 'pagenum="0"' \
--form 'pagesize="50"'
```
