---
description: >-
  Best practices for managing user access in large companies with multiple
  databases
---

# Adding New Users in Companies with Multiple Databases

### **Introduction:**&#x20;

Large companies and teams with multiple databases may have a need to restrict access to certain databases while allowing access to others. In order to manage access to databases effectively, it is important to establish best practices for adding users to individual databases.

In these cases, new users should always be given access to one database at a time via the Database-specific [Users](https://app.bugsplat.com/v2/settings/database/users?database) page (outlined below in pink).  Do not use the Company-wide [Manage Users](https://app.bugsplat.com/v2/settings/company/users) page (outlined below in yellow) as it grants access to all Company Databases.

<figure><img src="../../.gitbook/assets/database-specific-users-page.png" alt=""><figcaption></figcaption></figure>

### Best Practices:

1. In cases where it's important to restrict new user access to specific databases, ask team members to add users via the Database-specific [Users](https://app.bugsplat.com/v2/settings/database/users?database) page instead of the Company-wide [Manage Users](https://app.bugsplat.com/v2/settings/company/users) page
   * Users added via the Database-specific Users page will only get access to that individual Database
2. Limit non-restricted users' (admins) access to multiple databases
   * When a non-restricted user sends an invite via the Manage Users page, they, by default, give access to all the databases they have unrestricted access to
   * Users added by non-restricted users are set by default as restricted

### Conclusion:&#x20;

Managing user access in large companies with multiple databases is crucial to ensure privacy, security, and compliance. By following these best practices, companies can avoid potential issues and ensure that users only have access to the databases they need.
