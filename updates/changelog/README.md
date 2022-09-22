# Changelog

List of recent product changes and updates for BugSplat. We also discuss these updates on [Twitter](https://twitter.com/bugsplatco).

Looking for older product updates?  View our [Archive](changelog-archive/) to view past updates.

## bugsplat-gdk update - September 22th, 2022

#### Release - v1.2.2

* See bugsplat-gdk changelog for details

## SendPdbsDotNet Update - September 20th, 2022

**Release - v3.0.0**

* Target .NET 4.7.2 and for TLS 1.2 support
* Improve support for Win32 credentials AP

## Web App Update - September 18th, 2022

**Release - v2.13.0**

* Don't display alerts for 401 responses
* Filter values in Email/User columns with clicks

## BugSplat Native Update - September 17th, 2022

**Release - v4.0.3**

* Support quiet mode for out-of-process crash reporting
* Update MyCrasher sample

#### Release - v4.0.0

* Remove calls to unsafe native functions and support drop in .NET BsSndRpt replacement
* Changes the arguments used to invoke BsSndRpt, BugSplatHD in order to support .NET drop-in replacement (BugSplatCrashHandler)

## BsSndRpt - September 17th, 2022

**Release - v4.0.3**

* Crash when BugSplatSharedMemory is not initialized

## Web App Update - September 15th, 2022

**Release - v2.12.2**

* Upgrade modal styles

## BsSndRpt - September 14th, 2022

**Release - v4.0.2**

* Set quiet mode from BugSplatSharedMemory

**Release - v4.0.1**

* Simplify SDK folder structure
* Update BsSndRpt icon

**Release - v4.0.1**

* Target v142 for compatibility
* Support single char flags for BugSplatCrashHandler compatibility
* **Breaking Changes:** BsSndRpt expects to be invoked with single character flags and is not backwards compatible with BugSplat.dll versions before 4.0\


## Web App Update - September 14th, 2022

**Release - v2.12.1**

* Improve progress bar colors

## Web App Update - September 13th, 2022

**Release - v2.12**

* Support preview of PlayStation attachments

****

**Release - v2.11.15**

* Modules table horizontal scrollbar



**Release - v2.11.14**

* Show attachments table loading indicator



**Release - v2.11.13**

* Add PlayStation crash types
* Crash table column resizing change
* Update to [Bootstrap 5](https://getbootstrap.com/docs/5.0/getting-started/introduction/)

## .NET Standard  Update - September 8th, 2022

#### Release - v4.0.1

* Updated to latest SendPdbs - v3.0.0.0
* Improve SDK folder layout
* Remove deprecated function set\_unexpected from bugsplat.h

#### Release - v3.1.0

* Add symbol uploads
* Symbol files should be uploaded with name of zip
* Support for symbol file signatures

## React Update - September 8th, 2022

#### Release - v1.0.3

* **Examples:** Include pages mode environment file

## React Update - September 7th, 2022

#### Release - v1.0.2

* **Examples:** my-react-crasher fails to load

## BugSplat Native Update - August 30th, 2022

#### Release - v4.0.0

* Remove calls to unsafe native functions and support drop in .NET BsSndRpt replacement
* Changes the arguments used to invoke BsSndRpt, BugSplatHD in order to support .NET drop-in replacement (BugSplatCrashHandler)

## Web App Update - August 29th, 2022

#### Release - v2.11.12

* Add React to onboarding tool

## Stack Converter - August 24th, 2022

#### Release - v2.0.3

* Handle electron native plugin symbols

## Web App Update - August 19th, 2022

#### Release - v2.11.11

* Show Active Thread ID on Crash page

## Stack Converter - August 17th, 2022

#### Release - v1.2.5

* Support for stack frames with win32 paths

## Terms and Service Update - August 10th, 2022

* Updates to BugSplat [Terms of Service](../../introduction/production/security-privacy-and-compliance/terms.md)

## Web App Update - August 5th, 2022

#### Release - v2.11.10

* Change Jira Assignee Label to Assignee ID

## bugsplat-ng update - August 3rd, 2022

#### Release - v14.0.2

* Update README.md

#### Release - v14.0.1

* Security Fixes

#### Release - v14.0.0

**Features**&#x20;

* Angular 14

**BREAKING CHANGES**&#x20;

* Updates to package version to stay in sync with Angular versions&#x20;
* feat: use bugsplatJs to keep track of desc, key, email, user&#x20;
  * Description, key, email, and user are now set only properties&#x20;

#### Release - v13.0.0

* Add README Section for Source Maps&#x20;
* Demo refresh chore(sample): improve symbol uploads&#x20;
* Provide ivy distribution&#x20;
* Allow overriding logger&#x20;

## bugsplat-gdk update - July 26th, 2022

#### Release - v1.2.1

* See bugsplat-gdk changelog for details

## bugsplat-gdk update - July 25th, 2022

#### Release - v1.1.2

* See bugsplat-gdk changelog for details

## Web App Update - July 17th, 2022

#### Release - v2.11.7

* Add .macdmp as an inline preview type

## Web App Update - July 2nd, 2022

#### Release - v2.11.6

* re-add support for linkDefectId with prefix

## bugsplat-gdk update - June 30th, 2022

#### Release - v1.1.1

* See bugsplat-gdk changelog for details

#### Release - v1.1.0

* See bugsplat-gdk changelog for details



## Web App Update - June 27th, 2022

#### Release - v2.11.5

* fix release workflow action

#### Release - v2.11.4

* platform SVGs are too large



## BugSplat-Unity Update - May 25th, 2022

#### Release - v2.3.0

* feat: use BugSplatOptions for symbol uploads
* feat: remove SendPdbs

## Web App Update - May 25th, 2022

#### Release - 2.11.3

* Crash page should show loading when reprocessing crash

## Web App Update - May 22nd, 2022

#### Release - 2.11.2

* Show company name in settings dropdown
* Show non-restricted dbs on Admin page

## BugSplat-Unity Update - May 16th, 2022

#### Release - v2.1.4...v2.2.0

* Feat: add support for Unity 2019

## Web App Update - May 16th, 2022

#### Release - 2.11.1

* Should not throw errors when no plans available for Upgrade

## Web App Update - May 15th, 2022

#### Release - 2.11.0

* Add Rejected 30 Day and Rejected 365 Day

## Web App Update - April 29th, 2022

#### Release - 2.10.6

* Wait for a response before finalizing uploads

#### Release - 2.10.5

* npm version should be added to package.json

## Web App Update - April 28th, 2022

#### Release - 2.10.4

* Enable close button after symbol upload

## Web App Update - April 28th, 2022

#### Release - 2.10.4

* Enable close button after symbol upload

## JS API Client - April 21th, 2022

#### Release - 2.0.0

* Replace Symbols API Client with Versions API Client
* Merged the Symbols and Versions APIs to a single Versions API and thus have removed the Symbols API client.

## Web App Update - April 20th, 2022

#### Release - 2.10.3

* Remove default sort on Versions page

#### Release - 2.10.2

* Fix logic miss with showFiltering

## Web App Update - April 19th, 2022

#### Release - 2.10.1

* Version ID column should be visible by default

#### Release - 2.10.0

* Combine Symbols and Versions page&#x20;

## Web App Update - April 14th, 2022

#### Release - 2.9.26

* Center graphic on restricted users lockout screen

#### Release - 2.9.25

* Center graphic on restricted users lockout screen

## Web App Update - April 11th, 2022

#### Release - 2.9.24

* Improve drag and drop hover and disabled states

## Web App Update - April 5th, 2022

#### Release - 2.9.23

* Add legacy pricing FAQ

## Web App Update - April 4th, 2022

#### Release - 2.9.21

* Settings page background should fill vertical space

## Web App Update - April 2nd, 2022

#### Release - 2.9.20

* Empty Key Crash text should not be cutoff

#### Release - 2.9.19

* Fix Key Crash button hover should not be white

#### Release - 2.9.18

* Empty Key Crash text should not be cutoff

## Web App Update - April 1st, 2022

#### Release - 2.9.17

* Don't show empty IP address globe

## Web App Update - March 30th, 2022

#### Release - 2.9.16

* Confetti cannon should wait to fire on databases that already contain crashes
* Stack key links should be disabled until crash is processed

## Web App Update - March 29th, 2022

#### Release - 2.9.15

* Add Link to Filter By IP Address

#### Release - 2.9.14

* Restricted Settings menu should navigate to Billing page

#### Release - 2.9.13

* Make stack key link clickable

## Web App Update - March 26th, 2022

#### Release - 2.9.12

* Remove crash id header information

## Web App Update - March 25th, 2022

#### Release - 2.9.11

* Fix ngb-filterable-dropdown styling issue

## Web App Update - March 24th, 2022

#### Release - 2.9.10

* Should provide enterprise users a contact us link in subscription nag

#### Release - 2.9.9

* Improve crash page header styles

#### Release - 2.9.8

* Improve markdown styles for code snippets in onboarding

#### Release - 2.9.7

* Improve app and onboarding top nav styles

#### Release - 2.9.6

* Add link to platform docs in onboarding
* Update 'Bobby' image in the footer

## Web App Update - March 23rd, 2022

#### Release - 2.9.5

* Fix onboarding sample z-index issue

#### Release - 2.9.4

* Show warning message to locked out restricted user

## Web App Update - March 21st, 2022

#### Release - 2.9.3

* Should not nag customers who pay via Invoice

#### Release - 2.9.2

* Don't Nag Enterprise CustomersS

## Web App Update - March 19th, 2022

#### Release - 2.9.1

* Show nag when customer has exceeded user and app usage

## Pricing Update - March 16th, 2022

#### Commercial Plans&#x20;

Release of new pricing plans.  Users who sign up for plans after March 16th, 2022 are governed by pricing detailed on the [Plans](https://www.bugsplat.com/plans/) page. &#x20;

Users who signed up for plans before March 16th, 2022 are governed by [legacy pricing plans](../../administration/billing/legacy-plans-guide.md).

Learn more by viewing the [Billing](../../administration/billing/) documents or contacting [sales@bugsplat.com](mailto:sales@bugsplat.com).

#### Free Plans

Terms for the [Solo Developer](../../administration/billing/free-plans-from-bugsplat/standard-free-plan.md), [Open Source](../../administration/billing/free-plans-from-bugsplat/bugsplat-free-accounts-for-not-for-profit-open-source-projects.md), [Indie Game Dev](../../administration/billing/free-plans-from-bugsplat/free-accounts-for-indie-game-development.md#overview), [Education](../../administration/billing/free-plans-from-bugsplat/bugsplat-free-accounts-for-students-and-teachers.md), and [Good Causes](../../administration/billing/free-plans-from-bugsplat/good-causes.md) terms have been updated as part of this new pricing plans release.

#### Release - 2.9.0

* Release 2022 pricing

#### Release - 2.8.27

* Update lockout flow to work with new billing page

#### Release - 2.8.26

* Settings page should display "Loading..." instead of restricted before app is loaded

## Web App Update - March 15th, 2022

#### Release - 2.8.25

* Wait for stripe webhook

## Web App Update - March 14th, 2022

#### Release - 2.8.24

* Fix fontawesome path
* fix Pricing TODOs

## Web App Update - March 11th, 2022

#### Release - 2.8.23

* Add Manage Apps page

#### Release - 2.8.22

* Fix routerLink not defined on pricing page

## Web App Update - March 9th, 2022

#### Release - 2.8.21

* Merge Company General and Company Billing

## Web App Update - March 9th, 2022

#### Release - 2.8.20

* Reset Apps, Users, Crashes when toggling plan

## Web App Update - March 8th, 2022

#### Release - 2.8.19

* Use API for Upgrade Modal Crashes Input Max

#### Release - 2.8.18

* Enterprise card should be paid by invoice

## Web App Update - March 2nd, 2022

#### Release - 2.8.17

* Update pricing modal to match new design

## Web App Update - February 28th, 2022

#### Release - 2.8.16

* Fix Font Awesome Math.Divide Warning

## Web App Update - February 23rd, 2022

#### Release - 2.8.13

* Add manage your plan link for legacy plans



## Web App Update - February 15th, 2022

#### Release - 2.8.12

* Redirect to Account page if Stripe Portal returns null

#### Release - 2.8.11

* Fix should not fail to parse Subscription API



## BugSplat Angular - February 10th, 2022

#### Release - 5.0.0

* Uploads source maps&#x20;
* Add README Section for Source Maps&#x20;
* Demo refresh
* chore(sample): improve symbol uploads
* fix: provide ivy distribution

## Web App Update - February 2nd, 2022

#### Release - 2.8.8

* Update Crash and Key Crash action column styles

#### Release - 2.8.7

* Move firstReported/lastReported to side column

## BugSplat-js - February 2nd, 2022

#### Release - 7.1.1

* Bump node-fetch from 2.6.1 to 2.6.7
* Add types for server response

#### Release - 7.1.0

* Add options FormDataParam
* Make database, application, version public for bugsplat-ng

## Web App Update - February 1st, 2022

#### Release - 2.8.6

* Update Crash and Key Crash action column styles

#### Release - 2.8.5

* Billing should show usage for currently selected company

#### Release - 2.8.4

* Update comment component padding

#### Release - 2.8.3

* Update event stream component styles

## BugSplatNative library - January 31st, 2022

#### Release - 3.6.0.13

* Added MiniDumper::setNotes(const \_\_wchar\_t\* szNotes), which sets the initial value of the crash report Notes field. Note that your users can edit this field using the BugSplat web application. A current version of SendPdbs is also required for this API change.

## Web App Update - January 31st, 2022

#### Release - 2.8.2

* Update crash page overview font-size to 0.875rem

## Web App Update - January 18th, 2022

#### Release - 2.8.1

* Improve loading state on Settings pages

## Web App Update - January 10th, 2022

#### Release - 2.7.6

* NET Standard welcome url should be /dot-net-standard

## Blog Post: [Crash Course in Crash Grouping](https://www.bugsplat.com/blog/product/crash-course-in-grouping/)

**Published January, 10th 2022**

Supporting large applications with enormous crash volumes can be a real pain in the hindquarters. Luckily, we've upgraded our tooling for developers so that they can group related crashes and better target their support efforts. In this post, we quickly go over how you can take advantage of these important new tools.

[Read the post -->](https://www.bugsplat.com/blog/product/crash-course-in-grouping/)

![](../../.gitbook/assets/crash-cours-grouping-header.png)



## Web App Update - January 5th, 2022

#### Release - 2.7.4

* Fix .NET Standard Platform Info Title and Image

#### Release - 2.7.5

* Add attachment preview support for '.crashlog' files

## Web App Update - January 4rd, 2022

#### Release - 2.7.3

* Attachments preview should not close when crash is processing

## Web App Update - January 3rd, 2022

First update of 2022!  How exciting! BugSplat wishes you a happy and healthy 2022!

{% embed url="https://media.giphy.com/media/NsKiCmWdA96V4w10N5/giphy.gif" %}

#### Release - 2.7.1

* Widen database and company selector on medium screens

{% content-ref url="changelog-archive/archive-2021.md" %}
[archive-2021.md](changelog-archive/archive-2021.md)
{% endcontent-ref %}

{% hint style="warning" %}
**Looking for older product updates?**  To get view past product updates please view our current [Changelog Archives](changelog-archive/).  If you don't see what you're looking for make sure to [Contact Us](../../administration/contact-us.md).
{% endhint %}

