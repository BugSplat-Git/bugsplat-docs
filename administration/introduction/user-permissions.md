# User Permissions

### Restricted vs. Non-Restricted

BugSplat provides the ability to restrict the permissions of selected team members.  There are two levels of users: **Restricted** and **Non-Restricted**. &#x20;

**Restricted** users are prohibited from accessing account settings, including billing, adding/removing database and users, and setting database options. &#x20;

**Non-Restricted** users act as account admins and have full access to all BugSplat features.

### Permissions

Permissions are enforced at the database level. &#x20;

A user may have unrestricted access to some databases while being restricted to others.  Additionally, users may not have any access at all to a set of databases within your company.  This allows you to set up users that only have visibility to certain company projects.

Managing users across multiple databases can be performed on the [Company/Manage Users page](https://app.bugsplat.com/v2/settings/company/users).  Adding or removing users on this page affects all databases where the current user is unrestricted. &#x20;

**Note:** new users added on the [Company/Manage Users page](https://app.bugsplat.com/v2/settings/company/users) will automatically be added as 'Restricted'.&#x20;

#### Managing user permissions for specific databases

To manage user permissions on a single database use the [Database/Users page](https://app.bugsplat.com/v2/settings/database/users). &#x20;

This page allows you to add or remove a user for the selected database.  In addition, you can use this page to change the user's restricted status for the selected database.&#x20;

![Request User Access](../../.gitbook/assets/users-page.png)

Finally,  access to user permissions may be limited by your subscription level.  If you don't see the ability to restrict users, make sure to upgrade to a [Business level subscription](https://www.bugsplat.com/plans/).&#x20;
