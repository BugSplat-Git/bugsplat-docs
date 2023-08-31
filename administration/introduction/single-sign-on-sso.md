# Single Sign-On (SSO)

Enhance your organization's security and user management capabilities with our Single Sign-On (SSO) support. By integrating with third-party identity providers via SAML and OpenID Connect protocols, we provide a robust and secure authentication mechanism, tailored especially for larger enterprises.

SSO not only centralizes user management but also fortifies security by reducing the number of attack vectors and potential password vulnerabilities. As organizations scale, implementing a unified and secure login process becomes paramount.

In addition to SSO, we also support GitHub sign-in. This feature is available on all plans and is controlled by individual users. Organizations cannot use it to centralize user management.  Learn more [here](password-settings-and-reset-options/).

## SSO Setup Parameters

To setup SSO, your corporate IT team needs the following information:

BugSplat's user pool ID is: `us-east-1_rZndGLwmO`

BugSplat's SSO domain prefix is [`https://cognito.bugsplat.com`](https://cognito.bugsplat.com/)`.` This means our SAML assertion endpoint is: [https://cognito.bugsplat.com/saml2/idpresponse](https://cognito.bugsplat.com/saml2/idpresponse)

Our SP urn is `urn:amazon:cognito:sp:us-east-1_rZndGLwmO`

### Skipping the BugSplat Login Dialog

SSO users can avoid the standard BugSplat login dialog (and thus avoid entering their email address) by saving a link with the following form:

[`https://cognito.bugsplat.com/authorize?idp_identifier={your corporate domain}&redirect_uri=https://app.bugsplat.com/cognito/redirect.php&response_type=code&client_id=2cto7q004s89cid304op4sfc&scope=email%20openid%20profile`](https://cognito.bugsplat.com/authorize?idp\_identifier=imprivata.com\&redirect\_uri=https://app.bugsplat.com/cognito/redirect.php\&response\_type=code\&client\_id=2cto7q004s89cid304op4sfc\&scope=email%20openid%20profile%20aws.cognito.signin.user.admin)

Replace {your corporate domain} with the domain name of your organization e.g. `acme.com`
