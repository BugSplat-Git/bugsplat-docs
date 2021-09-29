# OAuth2

BugSplat supports authenticating via OAuth2 in addition to the username/password authentication described in our [API docs](api.md). Currently, only the OAuth2 client credentials flow is supported. If you are interested in other OAuth2 authentication methods, please reach out to [support@bugsplat.com](mailto:support@bugsplat.com).

A reference client implementation can be found in our [@bugsplat/js-api-client](https://github.com/BugSplat-Git/bugsplat-js-api-client/blob/1ebf36453d275ed62d6e5a4bb4249ef17efbc929/src/common/client/oauth-client-credentials-api-client/oauth-client-credentials-api-client.ts) repo.

### Client Credentials

To authenticate via OAuth2 client credentials you will need to create a Client Id and Client Secret pair on the Integrations tab on the [Options](https://app.bugsplat.com/v2/options?tab=integrations) page.

![Adding a New Integration](../../../.gitbook/assets/integration.gif)

Copy the Client Id and Client Secret to a secure location. Once you've dismissed the dialog the Client Secret will not be displayed again.

#### Authorize

The Client Id and Client Secret are used in a POST to the server and will return `access_token` and a `token_type`. The [Authorize](oauth2.md#authorize-1) endpoint is described below.

#### Headers

Once an `access_token` and `token_type` have been acquired they can be used in any of the API requests outlined in our [API docs](api.md). To make an authenticated request to one of BugSplat's API endpoints add a header with a key of `Authorization` and a value of `${token_type} ${access_token}`.

{% api-method method="post" host="https://app.bugsplat.com" path="/oauth2/authorize" %}
{% api-method-summary %}
Authorize
{% endapi-method-summary %}

{% api-method-description %}
Exchange a Client Id and Client Secret for a bearer token.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-form-data-parameters %}
{% api-method-parameter name="scope" type="string" required=true %}
OAuth2 scope, currently only **restricted** is the only available scope
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
The Client Secret created above
{% endapi-method-parameter %}

{% api-method-parameter name="client\_id" type="string" required=true %}
The Client Id created above
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
OAuth2 grant type, in this case, **client\_credentials**
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9[...]"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

