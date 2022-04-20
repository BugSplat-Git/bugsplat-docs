---
description: API Documentation for the BugSplat Defect Endpoint
---

# Defect

Operations related to BugSplat's connection with the defect tracker configured on the specified database.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/logDefect" method="get" summary="Defect" %}
{% swagger-description %}
Returns information regarding linked defects for a given crash (using id) or crash group (using stackKeyId).
{% endswagger-description %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database to query
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" %}
Crash group to query
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="number" %}
Crash Id to query
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "current_server_time": 1629833567,
    "message": "success",
    "id": 1,
    "defectId": 1

}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/logDefect" method="post" summary="Defect" %}
{% swagger-description %}
Create an issue in the associated defect tracker
{% endswagger-description %}

{% swagger-parameter in="body" name="stackKeyId" type="number" %}
Id of the crash group being used to create a new issue
{% endswagger-parameter %}

{% swagger-parameter in="body" name="id" type="number" %}
Id of the crash being used to create a new issue
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" %}
BugSplat database containing the crashId or stackKeyId
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "current_server_time": 1629834214,
    "message": "success",
    "id": 1,
    "defectId": 1
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/logDefect" method="delete" summary="Defect" %}
{% swagger-description %}
Removes a defect association from BugSplat. This method will not remove the issue from the associated issue tracker.
{% endswagger-description %}

{% swagger-parameter in="query" name="stackKeyId" type="string" %}
Crash group for which the defect association will be removed
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="number" %}
Crash id for which the defect association will be removed
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing the crashId or stackKeyId
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "current_server_time": 1629834172,
    "message": "success",
    "id": 1
}
```
{% endswagger-response %}
{% endswagger %}
