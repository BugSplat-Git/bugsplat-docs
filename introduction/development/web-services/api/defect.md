---
description: API Documentation for the BugSplat Defect Endpoint
---

# Defect

Operations related to BugSplat's connection with the defect tracker configured on the specified database.

## Get Defect

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/logDefect`

Returns information regarding linked defects for a given crash (using id) or crash group (using stackKeyId).

#### Query Parameters

| Name       | Type   | Description                |
| ---------- | ------ | -------------------------- |
| database   | string | BugSplat database to query |
| stackKeyId | number | Crash group to query       |
| id         | number | Crash Id to query          |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "current_server_time": 1629833567,
    "message": "success",
    "id": 1,
    "defectId": 1
}
```
{% endtab %}
{% endtabs %}

## Create Defect

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/logDefect`

Create an issue in the associated defect tracker

#### Request Body

| Name       | Type   | Description                                            |
| ---------- | ------ | ------------------------------------------------------ |
| stackKeyId | number | Id of the crash group being used to create a new issue |
| id         | number | Id of the crash being used to create a new issue       |
| database   | string | BugSplat database containing the crashId or stackKeyId |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "current_server_time": 1629834214,
    "message": "success",
    "id": 1,
    "defectId": 1
}
```
{% endtab %}
{% endtabs %}

## Remove Defect

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/logDefect`

Removes a defect association from BugSplat. This method will not remove the issue from the associated issue tracker.

#### Query Parameters

| Name       | Type   | Description                                                  |
| ---------- | ------ | ------------------------------------------------------------ |
| stackKeyId | string | Crash group for which the defect association will be removed |
| id         | number | Crash id for which the defect association will be removed    |
| database   | string | BugSplat database containing the crashId or stackKeyId       |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "current_server_time": 1629834172,
    "message": "success",
    "id": 1
}
```
{% endtab %}
{% endtabs %}
