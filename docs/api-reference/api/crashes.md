---
description: API Documentation for the BugSplat Crashes Endpoint
---

# Crashes

{% hint style="info" %}
This endpoint supports paging, filtering, and grouping queries. For more information on paging, filtering, and grouping, please visit this [link](../paging-filtering-and-grouping.md).
{% endhint %}

## Get Crashes

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/crashes`

Get a list of crashes for a database. This query supports paging, filtering, and grouping. All of the property keys in the Rows object can be used as column values for filtering and grouping, e.g., id, stackKey, appName, ipAddress, etc.

#### Query Parameters

| Name     | Type   | Description                             |
| -------- | ------ | --------------------------------------- |
| database | string | BugSplat database containing crash data |

{% tabs %}
{% tab title="200 " %}
```json
{
  "database": "Fred",
  "pageData": {
    "defectTracker": true,
    "defectTrackerType": "GitHub"
  },
  "rows": [
    {
      "id": "140612",
      "status": "0",
      "stackId": "19187",
      "stackKey": "myConsoleCrasher+0x12b5",
      "stackKeyId": "9579",
      "appName": "myConsoleCrasher",
      "appVersion": "1.048",
      "appDescription": "appKey",
      "userDescription": "A default user description",
      "user": "TestUser",
      "email": "TestUser@bugsplat.com",
      "IpAddress": "34.225.87.xxxx",
      "crashTime": "2025-10-30T20:52:24Z",
      "defectId": null,
      "defectUrl": "",
      "defectLabel": "",
      "skDefectId": null,
      "skDefectUrl": "",
      "skDefectLabel": "",
      "Comments": "",
      "skComments": "",
      "crashTypeId": "1",
      "exceptionCode": "c0000005",
      "exceptionMessage": "Access violation",
      "attributes": "{}",
      "lineNumber": null,
      "groupByCount": null
    }
  ]
}
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

## Delete Crashes

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/crashes.php`

Delete one or more crashes by ID. The maximum batch size per call is 50.

#### Query Parameters

| Name     | Type   | Description                             |
| -------- | ------ | --------------------------------------- |
| database | string | BugSplat database containing crash data |
| ids      | string | Comma-separated list of crash IDs       |

{% tabs %}
{% tab title="200 " %}
```
(empty response body)
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location --request DELETE 'https://app.bugsplat.com/api/crashes.php?database=Fred&ids=141072%2C141070' \
--header 'Authorization: Bearer ••••••'
```
