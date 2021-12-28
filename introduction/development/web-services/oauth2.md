# OAuth2

BugSplat supports authenticating via OAuth2 in addition to the username/password authentication described in our [API docs](api.md). Currently, only the OAuth2 client credentials flow is supported. If you are interested in other OAuth2 authentication methods, please reach out to [support@bugsplat.com](mailto:support@bugsplat.com).

A reference client implementation can be found in our [@bugsplat/js-api-client](https://github.com/BugSplat-Git/bugsplat-js-api-client/blob/1ebf36453d275ed62d6e5a4bb4249ef17efbc929/src/common/client/oauth-client-credentials-api-client/oauth-client-credentials-api-client.ts) repo.

### Client Credentials

To authenticate via OAuth2 client credentials you will need to create a Client Id and Client Secret pair on the Integrations tab on the ‘[Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)’ page.

![Adding a New Integration](../../../.gitbook/assets/integration.gif)

Copy the Client Id and Client Secret to a secure location. Once you've dismissed the dialog the Client Secret will not be displayed again.

#### Authorize

The Client Id and Client Secret are used in a POST to the server and will return `access_token` and a `token_type`. The [Authorize](oauth2.md#authorize-1) endpoint is described below.

#### Headers

Once an `access_token` and `token_type` have been acquired they can be used in any of the API requests outlined in our [API docs](api.md). To make an authenticated request to one of BugSplat's API endpoints add a header with a key of `Authorization` and a value of `${token_type} ${access_token}`.

{% swagger baseUrl="https://app.bugsplat.com" path="/oauth2/authorize" method="post" summary="Authorize" %}
{% swagger-description %}
Exchange a Client Id and Client Secret for a bearer token.
{% endswagger-description %}

{% swagger-parameter in="body" name="scope" type="string" %}
OAuth2 scope, currently only 

**restricted**

 is the only available scope
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_secret" type="string" %}
The Client Secret created above
{% endswagger-parameter %}

{% swagger-parameter in="body" name="client_id" type="string" %}
The Client Id created above
{% endswagger-parameter %}

{% swagger-parameter in="body" name="grant_type" type="string" %}
OAuth2 grant type, in this case, 

**client_credentials**
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9[...]"
}
```
{% endswagger-response %}
{% endswagger %}
