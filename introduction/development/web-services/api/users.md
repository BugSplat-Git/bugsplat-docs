---
description: API Documentation for the BugSplat Users Endpoint
---

# Users

{% hint style="info" %}
This endpoint supports paging and filtering queries. More information paging, filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list of users, add a user, or remove a user from a specified database.

## Users

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/user/users`

Returns a list of users and access rights for the specified database.

#### Query Parameters

| Name     | Type   | Description                        |
| -------- | ------ | ---------------------------------- |
| database | string | BugSplat database containing users |

{% tabs %}
{% tab title="200 " %}
```json
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
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/user/users?database=fred' \
--header 'Authorization: Bearer ••••••'
```

## Users

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/user/users`

Adds a new user to the specified database.

#### Request Body

| Name     | Type   | Description                               |
| -------- | ------ | ----------------------------------------- |
| database | string | BugSplat database to add the user to      |
| username | string | Email of user to be added to the database |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "username": "fred@bugsplat.com",
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/user/users' \
--header 'xsrf-token: ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA' \
--header 'Cookie: user=fred%40bugsplat.com; PHPSESSID=4g3pr9rdtehoddac9ohrt1qrc3cehg54; xsrf-token=ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA' \
--form 'database="fred"' \
--form 'username="name@test.com"'
```

## Users

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/user/users`

Removes a user from the specified database. The authenticated user must not be restricted for this call to succeed.

#### Query Parameters

| Name     | Type   | Description                              |
| -------- | ------ | ---------------------------------------- |
| database | string | BugSplat database that contains the user |
| username | number | Email of the user to remove              |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "uId": 55411,
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location --request DELETE 'https://app.bugsplat.com/api/user/users?database=fred&username=name%40test.com' \
--header 'xsrf-token: ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA' \
--header 'Cookie: user=fred%40bugsplat.com; PHPSESSID=4g3pr9rdtehoddac9ohrt1qrc3cehg54; xsrf-token=ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA'
```
