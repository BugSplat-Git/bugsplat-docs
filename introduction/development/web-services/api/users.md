---
description: API Documentation for the BugSplat Users Endpoint
---

# Users

{% hint style="info" %}
This endpoint supports paging, and filtering queries. More information paging filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list of users, add a user, or remove a user from a specified database.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/users" method="get" summary="Users" %}
{% swagger-description %}
Returns a list of users and access rights for the specified database.
{% endswagger-description %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing users
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
  {
    "Database": "Fred",
    "PageData": null,
    "Rows": [
      {
        "uId": "55411",
        "username": "fred@bugsplat.com",
        "lastLogin": "2021-08-25T21:02:56Z",
        "Restricted": "1"
      }
    ]
  }
]
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/users" method="post" summary="Users" %}
{% swagger-description %}
Adds a new user to the specified database.
{% endswagger-description %}

{% swagger-parameter in="body" name="username" type="string" %}
Email of user to be added to the database
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" %}
BugSplat database to add the user to
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "username": "fred@bugsplat.com",
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/users" method="delete" summary="Users" %}
{% swagger-description %}
Removes a user from the specified database. The authenticated user must not be a restricted user for this call to succeed.
{% endswagger-description %}

{% swagger-parameter in="query" name="uId" type="number" %}
Id of the user to remove
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database that contains the user
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "uId": 55411,
}
```
{% endswagger-response %}
{% endswagger %}

