---
description: API Documentation for the BugSplat User (GDPR) Endpoint
---

# User (GDPR)

User data endpoints for GDPR compliance. Get or delete the data associated with a specified user.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/user/userData" method="get" summary="User Data" %}
{% swagger-description %}
Returns crash reports containing specified email, username, or IP Address. Only one of the following query params can be specified at a time: email, username, ipAddress.
{% endswagger-description %}

{% swagger-parameter in="query" name="ipAddress" type="string" %}
IP Address of user to search for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="string" %}
Username of user to search for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="email" type="string" %}
Email of user to search for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database to query for user data
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "Status": "Success",
    "crashes": [
        {
            "id": 1,
            "crashDate": "2021-08-25 20:00:00",
            "user": "Fred",
            "email": "fred@bugsplat.com",
            "ipAddress": "127.0.0.1"
        }
    ]
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/user/userData" method="delete" summary="User Data" %}
{% swagger-description %}
Destroys the PII data associated with each matching crash report. This process will obfuscate email, username, and IP Address and delete the crash upload file. Only one of the following query params can be specified at a time: email, username, ipAddress.
{% endswagger-description %}

{% swagger-parameter in="query" name="ipAddress" type="string" %}
IP Address of the user whose data will be deleted
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="string" %}
Username of the user whose data will be deleted
{% endswagger-parameter %}

{% swagger-parameter in="query" name="email" type="string" %}
Email of the user whose data will be deleted
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing user data
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "Status": "Success"
}
```
{% endswagger-response %}
{% endswagger %}
