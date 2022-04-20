---
description: API Documentation for the BugSplat Events Endpoint
---

# Events

Get a list of events, or post a new event for a given Crash ID or Crash Group (Stack Key) ID.

{% swagger method="get" path="/api/events" baseUrl="https://app.bugsplat.com" summary="Events" %}
{% swagger-description %}
Get all events associated with a Crash Id or Crash Group (Stack Key).
{% endswagger-description %}

{% swagger-parameter in="query" name="stackKeyId" type="number" %}
Crash group to fetch events for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="crashId" type="number" %}
Crash id to fetch events for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
Name of the database containing the specified Crash Id or Crash Group (Stack Key).
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
  "status": "success",
  "events": [
    {
      "id": "118",
      "type": "Comment",
      "timestamp": "2022-02-02T19:25:11Z",
      "uId": "27581",
      "username": "bobby@bugsplat.com",
      "firstName": "Bobby",
      "lastName": "",
      "message": "testing 123"
    }
  ]
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/api/events/comment" baseUrl="https://app.bugsplat.com" summary="Comment" %}
{% swagger-description %}
Post a new comment for a given Crash Id or Crash Group (Stack Key).
{% endswagger-description %}

{% swagger-parameter in="query" name="message" type="string" required="true" %}
Markdown string containing the body of the comment to post.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" %}
Crash group to fetch events for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="crashId" type="number" %}
Crash Id to fetch events for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
Name of the database that contains the specified Crash Id or Crash Group (Stack Key).
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
    "status": "success",
    "messageId": 127
}
```
{% endswagger-response %}
{% endswagger %}
