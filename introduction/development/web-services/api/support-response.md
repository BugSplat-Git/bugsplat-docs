---
description: API Documentation for the BugSplat Support Response Endpoints
---

# Support Response

Get or set the contents of the end-user [Support Response page](../../../production/setting-up-custom-support-responses.md) that is loaded by the BugSplat [Windows Native C++](../../../getting-started/integrations/desktop/cplusplus/) and [Windows .NET Framework](../../../getting-started/integrations/desktop/windows-dot-net-framework.md) SDKs.

## Get Message

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/techsupport/message`

Returns the subject and raw markdown body contents for a Support Response page that has been configured for a specified stackKeyId and appKey.

#### Query Parameters

| Name                                         | Type   | Description                                                                                                                                |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| appKey                                       | string | An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages. |
| stackKeyId<mark style="color:red;">\*</mark> | number | The Crash Group (Stack Key) ID of the specified crash                                                                                      |
| database                                     | string | BugSplat database containing the specified crash                                                                                           |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "stackKeyId": 5524,
    "stackKey": "myConsoleCrasher!MemoryException(153)",
    "appKey": "*Default*",
    "subject": "",
    "content": "",
    "allkeys": null
}
```
{% endtab %}
{% endtabs %}

## Add Message

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/techsupport/message`

Create a new Support Response page for a specified stackKeyId and appKey.

#### Query Parameters

| Name                                         | Type   | Description                                                                                                                                |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| content<mark style="color:red;">\*</mark>    | string | Markdown contents of the Support Response page's body.                                                                                     |
| subject<mark style="color:red;">\*</mark>    | string | The subject line to be displayed to an end-user.                                                                                           |
| appKey                                       | string | An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages. |
| stackKeyId<mark style="color:red;">\*</mark> | number | The Crash Group (Stack Key) ID of the specified crash                                                                                      |
| database                                     | string | BugSplat database containing the specified crash                                                                                           |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "stackKeyId": 0,
    "appKey": "*Default*",
    "subject": "Default Message",
    "content": "This is the default support response for crashes posted to the Fred database.  It will be displayed if there is no stack key specific support response available."
}
```
{% endtab %}
{% endtabs %}

## Update Message

<mark style="color:orange;">`PUT`</mark> `https://app.bugsplat.com/api/techsupport/message`

Update a Support Response page for a specified stackKeyId and appKey.

#### Query Parameters

| Name                                         | Type   | Description                                                                                                                                |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| content<mark style="color:red;">\*</mark>    | string | Markdown contents of the Support Response page's body.                                                                                     |
| subject<mark style="color:red;">\*</mark>    | string | The subject line to be displayed to an end-user.                                                                                           |
| appKey                                       | string | An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages. |
| stackKeyId<mark style="color:red;">\*</mark> | number | The Crash Group (Stack Key) ID of the specified crash                                                                                      |
| database                                     | string | BugSplat database containing the specified crash                                                                                           |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "stackKeyId": 0,
    "appKey": "*Default*",
    "subject": "Default Message",
    "content": "This is the default support response for crashes posted to the Fred database.  It will be displayed if there is no stack key specific support response available."}
```
{% endtab %}
{% endtabs %}

## Delete Message

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/techsupport/message`

Delete a Support Response page for a specified stackKeyId and appKey.

#### Query Parameters

| Name                                         | Type   | Description                                                                                                                                |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| appKey                                       | string | An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages. |
| stackKeyId<mark style="color:red;">\*</mark> | number | The Crash Group (Stack Key) ID of the specified crash                                                                                      |
| database                                     | string | BugSplat database containing the specified crash                                                                                           |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "stackKeyId": 5524,
    "appKey": "en-US",
    "affected_rows": 1
}
```
{% endtab %}
{% endtabs %}
