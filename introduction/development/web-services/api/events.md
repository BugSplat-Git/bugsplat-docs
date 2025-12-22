---
description: API Documentation for the BugSplat Events Endpoint
---

# Events

Get a list of events, or post a new event for a given Crash ID or Crash Group (Stack Key) ID.

## Get Events

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/events`

Get all events associated with a Crash Group (Stack Key).

#### Query Parameters

| Name       | Type   | Description                                                                        |
| ---------- | ------ | ---------------------------------------------------------------------------------- |
| database   | string | Name of the database containing the specified Crash Id or Crash Group (Stack Key). |
| stackKeyId | number | Crash group to fetch events for.                                                   |

{% tabs %}
{% tab title="200: OK " %}
```json
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
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/events?database=fred&stackKeyId=10315' \
--header 'Authorization: Bearer ••••••'
```

## Add Comment

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/events/comment`

Post a new comment for a given Crash Group (Stack Key).

#### Query Parameters

| Name                                      | Type   | Description                                                                           |
| ----------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| database                                  | string | Name of the database that contains the specified Crash Id or Crash Group (Stack Key). |
| message<mark style="color:red;">\*</mark> | string | Markdown string containing the body of the comment to post.                           |
| stackKeyId                                | number | Crash group to comment on                                                             |

{% tabs %}
{% tab title="200: OK " %}
```json
{
    "status": "success",
    "messageId": 127
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location --request POST 'https://app.bugsplat.com/api/events/comment?database=fred&stackKeyId=10315&message=hello%20from%20**postman**!%20%F0%9F%91%8B' \
--header 'Authorization: Bearer ••••••' 
```
