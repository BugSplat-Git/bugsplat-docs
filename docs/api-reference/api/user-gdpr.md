---
description: API Documentation for the BugSplat User (GDPR) Endpoint
---

# User (GDPR)

User data endpoints for GDPR compliance. Get or delete the data associated with a specified user.

## Get User Data

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/user/userData`

Returns crash reports containing specified email, username, or IP Address. Only one of the following query params can be specified at a time: email, username, ipAddress.

#### Query Parameters

| Name      | Type    | Description                                                               |
| --------- | ------- | ------------------------------------------------------------------------- |
| ipAddress | string  | IP Address of user to search for                                          |
| username  | string  | Username of user to search for                                            |
| email     | string  | Email of user to search for                                               |
| database  | string  | BugSplat database to query for user data                                  |
| companyId | integer | Company identifier.  If supplied, all company databases will be queried.  |

{% tabs %}
{% tab title="200 " %}
```json
{
    "Status": "Success",
    "crashes": [
        {
            "database": "FredsDatabase",
            "id": 1,
            "crashDate": "2021-08-25 20:00:00",
            "user": "Fred",
            "email": "fred@bugsplat.com",
            "ipAddress": "127.0.0.1"
        }
    ]
}
```
{% endtab %}
{% endtabs %}

## Delete User Data

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/user/userData`

Destroys the PII data associated with each matching crash report. This process will obfuscate email, username, and IP Address and delete the crash upload file. Only one of the following query params can be specified at a time: email, username, ipAddress.

#### Query Parameters

| Name      | Type    | Description                                                                    |
| --------- | ------- | ------------------------------------------------------------------------------ |
| ipAddress | string  | IP Address of the user whose data will be deleted                              |
| username  | string  | Username of the user whose data will be deleted                                |
| email     | string  | Email of the user whose data will be deleted                                   |
| database  | string  | A single BugSplat database to process                                          |
| companyId | integer | Company identifier.  If supplied all company-owned databases will be processed |

{% tabs %}
{% tab title="200 " %}
```json
{
    "Status": "Success"
}
```
{% endtab %}
{% endtabs %}
