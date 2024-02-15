# Single Sign-On (SSO)

Enhance your organization's security and user management capabilities with our Single Sign-On (SSO) support. By integrating with third-party identity providers via SAML and OpenID Connect protocols, we provide a robust and secure authentication mechanism, tailored especially for larger enterprises.

SSO not only centralizes user management but also fortifies security by reducing the number of attack vectors and potential password vulnerabilities.&#x20;

In addition to SSO, BugSplat also supports federated authentication using Google or GitHub sign-in. This feature is available on all plans and is controlled by individual users. Organizations cannot use it to centralize user management.  Learn more [here](password-settings-and-reset-options/).

## SSO Authentication&#x20;

To setup SSO, your corporate IT team needs the following information:

BugSplat's user pool ID is: `us-east-1_rZndGLwmO`

BugSplat's SSO domain prefix is [`https://cognito.bugsplat.com`](https://cognito.bugsplat.com/) This means our SAML assertion endpoint is: [https://cognito.bugsplat.com/saml2/idpresponse](https://cognito.bugsplat.com/saml2/idpresponse)

Our SP urn is `urn:amazon:cognito:sp:us-east-1_rZndGLwmO`

BugSplat support will configure SSO for your organization when supplied with the SAML metadata document generated from your organization.  Please send this file to support@bugsplat.com.

Need help setting up your integration?  See the following instructions for some popular IdPs.  These links are shamelessly copied from the AWS Cognito docs.  They discuss both the IdP setup along with the Cognito setup.   (BugSplat will perform the Cognito setup on your behalf.)

* Active Directory Federation Services (ADFS):
  * [Building ADFS Federation for your Web App using Amazon Cognito User Pools](https://aws.amazon.com/blogs/mobile/building-adfs-federation-for-your-web-app-using-amazon-cognito-user-pools/)
  * [How do I set up AD FS as a SAML identity provider with an Amazon Cognito user pool?](https://aws.amazon.com/premiumsupport/knowledge-center/cognito-ad-fs-saml/)
* Auth0: [How do I set up Auth0 as a SAML identity provider with an Amazon Cognito user pool?](https://aws.amazon.com/premiumsupport/knowledge-center/auth0-saml-cognito-user-pool/)
* Azure Active Directory: [Setup Amazon Cognito User Pool with an Azure AD identity provider to perform single sign-on (SSO) authentication in mobile app](https://medium.com/@zippicoder/setup-aws-cognito-user-pool-with-an-azure-ad-identity-provider-to-perform-single-sign-on-sso-7ff5aa36fc2a)
* Centrify: [Adding and configuring a Custom SAML application](https://docs.centrify.com/Content/Applications/AppsCustom/AddConfigSAML.htm)
* G-Suite: [Set up your own custom SAML application](https://support.google.com/a/answer/6087519?hl=en)
* Okta: [Okta as a SAML identity provider](https://repost.aws/knowledge-center/cognito-okta-saml-identity-provider)&#x20;
* OneLogin: [How do I set up OneLogin as a SAML identity provider with an Amazon Cognito user pool?](https://aws.amazon.com/premiumsupport/knowledge-center/cognito-saml-onelogin/)
* PingFederate: [PingOne for Enterprise Administration Guide: Add or update a SAML application](https://documentation.pingidentity.com/pingone/employeeSsoAdminGuide/index.shtml#enableAppWithoutURL.html)

Note:  SSO integration is a premium feature that requires a BugSplat Enterprise subscription.

## SSO Authorization

You can optionally provide access to BugSplat databases by associating groups with each user that match groups assigned to your BugSplat databases.  If no user groups are provided, users can authenticate with BugSplat but won't have access to any company databases until you add them using the [Database Users](https://app.bugsplat.com/v2/database/users) or [Company Manage Users](https://app.bugsplat.com/v2/company/users) pages.&#x20;

Group memberships for each user are passed to BugSplat in the SAML 2.0 assertion. The “groups” attribute statement must include the name of each BugSplat database group to which the user should be added.

Groups provided by SSO are matched to groups defined for each of your databases on the Integrations/SSO page.  Each database has a group name for access and administration.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>SSO Group Definition</p></figcaption></figure>

For example, you might assign the Groups "BsAccess" and "BsAdmin" to each BugSplat database.  Then, users who should have administrator rights would be assigned to the "BsAdmin" role, and users who needed only regular access would be assigned the "BsAccess" role.

Note that the SSO groups will be copied from the current database when creating a new database.  So typically, once they are set up, no further group definition will be required.

If SSO Groups are provided to BugSplat, they will override any existing database access permissions.  At login time, a user's permissions are reset according to the group rules.  After a user logs into BugSplat, you can view their updated access rules on the [Database Users](https://app.bugsplat.com/v2/database/users) or [Company Manage Users](https://app.bugsplat.com/v2/company/users) pages.

### Skipping the BugSplat Login Dialog

SSO users can avoid the standard BugSplat login dialog (and thus avoid entering their email address) by saving a link with the following form:

[`https://cognito.bugsplat.com/authorize?idp_identifier={your corporate domain}&redirect_uri=https://app.bugsplat.com/cognito/redirect.php&response_type=code&client_id=2cto7q004s89cid304op4sfc&scope=email%20openid%20profile`](https://cognito.bugsplat.com/authorize?idp\_identifier=imprivata.com\&redirect\_uri=https://app.bugsplat.com/cognito/redirect.php\&response\_type=code\&client\_id=2cto7q004s89cid304op4sfc\&scope=email%20openid%20profile%20aws.cognito.signin.user.admin)

Replace {your corporate domain} with the domain name of your organization e.g. `acme.com`
