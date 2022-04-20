---
description: API Documentation for the BugSplat Databases Endpoint
---

# Databases

Operations related to a user or company's databases in BugSplat. Get a list of databases for the currently authenticated user or company, create a new database, transfer a database's ownership to another company, or delete a database.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/databases" method="get" summary="Databases" %}
{% swagger-description %}
Returns databases for the current user or a company specified by companyId.
{% endswagger-description %}

{% swagger-parameter in="query" name="companyId" type="number" %}
ID of the company that owns the databases
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[{
    "dbName": "Fred",
    "companyId": "545",
    "companyName": "BugSplat Public Testing",
    "Volume30Day": "1075",
    "Volume365Day": "8868",
    "CrashDataDays": "6544"}]
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/databases" method="post" summary="Databases" %}
{% swagger-description %}
Create a new database for the current user. Default values for the new database are copied from the current database.
{% endswagger-description %}

{% swagger-parameter in="body" name="database" type="string" required="true" %}
Name of the database being created
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "database": "fred"}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/databases" method="put" summary="Databases" %}
{% swagger-description %}
Transfer the ownership of a database to a different company.
{% endswagger-description %}

{% swagger-parameter in="body" name="companyId" type="number" %}
ID of the company the database is being transferred to
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" %}
Name of the database being transferred
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "database": "fred",
    "companyId": 25
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/databases" method="delete" summary="Databases" %}
{% swagger-description %}
Deletes a database and all associated data from BugSplat. This action is irreversible
{% endswagger-description %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database to delete
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "database": "fred",
    "deleted":true
}
```
{% endswagger-response %}
{% endswagger %}
