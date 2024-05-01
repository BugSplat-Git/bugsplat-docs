---
description: To create or update a defect tracker integration, use the following endpoint.
---

# Defect Tracker Options

## DefectTracking&#x20;

<mark style="color:green;">`GET`</mark> `https://app.bugsplat.com/api/options/defectTracker`

Gets the current defect tracking options.  The results will vary depending on the defect tracker type.

{% tabs %}
{% tab title="200" %}
```
{status: 'success', 
 database: {database}, 
 defectTracker: {defect tracker type},
 options: {list of all defect tracker-specific options}
}
```
{% endtab %}
{% endtabs %}



<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/options/defectTracker`

Sets defect tracking options.  The available fields vary based on the defect tracker type. &#x20;

#### Request Body

| Name                           | Type   | Description                                                        |
| ------------------------------ | ------ | ------------------------------------------------------------------ |
| defectTracker                  | string | Required type of defect tracker.  e.g. "Jira", "Azure DevOps", etc |
| Any defect tracker option name | string | Optional: Used to change any of the defect tracker options         |

{% tabs %}
{% tab title="200 " %}
```
{status: 'success', 
 database: {database}, 
 defectTracker: {defect tracker type},
 options: {list of all defect tracker-specific options}
}
```
{% endtab %}
{% endtabs %}
