# Archive 2020

{% hint style="info" %}
**Looking for the current changelog?**  To get current product updates please view our current [Changelog](../) or follow us on [Twitter](https://twitter.com/BugSplatCo/) (we post updates there as well).
{% endhint %}

## Web App Update 21-Dec-2020

* Added support for Assembla

## Web App Update 16-Dec-2020

* Style updates for Crash page
* SStyle updates for Log Defect modal
* SFix bug with Time Frame filtering on Key Crash page

## Web App Update 14-Dec-2020

* Fix for Group By control not closed by outside click
* Added support for exporting Group By results on Crashes page
* Added support for global Applications, Versions and Time Frame filters when exporting from the Crashes page

## Web App Update 12-Dec-2020

* Added ability to persist time frame filter between sessions

## Web App Update 09-Dec-2020

* Replace Database, Application and Version dropdowns with Breadcrumbs
* Add support for time frame 'All'
* Style improvements

## Web App Update 08-Dec-2020

* Fix for bug where global filters were not being honored on Crashes page

## Web App Update 04-Dec-2020

* Added crash attachments to row details view on Crashes page

## Web App Update 2-Dec-2020

* Released Group By feature on Crashes page
* Improved Table Search by making search columns searchable

## Ngb-Filterablep-Dropdown Update 1-Dec-2020

* Added ability to use any template as dropdown toggle
* Bug fixes
* Released version 6.1.2

## AWS Status Update 26-November-2020

* BugSplat application services have been restored to full health following the AWS outage. Happy Thanksgiving everyone!

## AWS Status Update 25-November-2020

* Update 7:00 pm est:BugSplat application services have recovered from the AWS outage which is not fully resolved. Note that until the AWS services are fully-restored, there is a continued risk of interruption of BugSplat services. See [here](https://status.aws.amazon.com) for additional details.
* BugSplat services are experiencing intermittent failures due to issues at our service provider AWS. Our team is monitoring and working to mitigate the effects. We will update here when the issues are resolved. Learn more at [AWS Service Health Dashboard](https://status.aws.amazon.com).

## Ngb-Filterablep-Dropdown Update 27-November-2020

* Added ability to set different selection modes for multi-selection which will hide or show Select All, Select None and Select Multiple
* Released version 6.0.0

## Web App Update 18-November-2020

* Updated look and feel of table row expansion
* Updated styles on Crash page

## Apple Silicon support! 16-November-2020

* A preview version of the BugSplat Mac SDK is available for testing at [https://github.com/BugSplat-Git/BugSplatMac](https://github.com/BugSplat-Git/BugSplatMac). This version contains our initial support for [Apple Silicon](https://www.apple.com/newsroom/2020/06/apple-announces-mac-transition-to-apple-silicon/), and we encourage customers to test it and give feedback.

## Ngb-Filterablep-Dropdown Update 13-November-2020

* Added ability to set custom toggle text
* Released version 5.0.0

## Breakpad/Crashpad Update 12-November-2020

* Upgraded processing of Breakpad and Crashpad reports so that function name references in the call stack now include the module name too. Existing customers may notice some call stacks are grouped in new stack keys as a result of this change.

## Web App Update 11-November-2020

* Fix BugSplat passing bad ID to Crash page
* Fix BugSplat checking subscription status on 404 page
* Fix broken image icon if user hasn't uploaded company logo
* Fix weird wrapping of Export component on Crashes page

## Web App Update 10-November-2020

* Addressed customer feedback to fix styles on Stack Explorer page

## Web App Update 9-November-2020

* Addressed customer feedback to use correct casing for Key Crash page header
* Fixed issue logging out

## Web App Update 5-November-2020

* Fix for Crash ID missing on Crash page on mobile devices
* Fix for version filters not being used on Key Crash page
* Fix for end users not being able to view Upload page without logging in first

## Web App Update 4-November-2020

* Updated look and feel of the navigation bar

## Web App Update 3-November-2020

* Defaulted Time Frame filter to Last 30 days in order to improve performance

## Web App Update 22-Oct-2020

* Released new Upload page for end users who were unable to post a crash report automatically
* Released Migrate page to assist companies migrating from their existing crash report solution to BugSplat

## Web App Update 10-October-2020

* Released new Password page

## Web App Update 09-October-2020

* Added new **Users Affected** column to [Summary](https://app.bugsplat.com/v2/summary) page. This column shows unique users who exprienced a crash associated with a Stack Key. More information on the Users Affected column can be found [here](https://www.bugsplat.com/blog/product/users-affected-by-crash/).

## ngb-filterable-dropdown update 08-October-2020

* Released version 4.0.0
* Added openChanged event that fires when the dropdown is opened or closed
* API updates to standardize naming conventions

## Web App Update 08-October-2020

* Updated Crash page comment widget styles
* Updated Key Crash page comment widget styles
* Fix for fussy comments field that sometimes reverted modifications
* Fix for bug where Group Stacks page displayed incorrect values due to non-unique stackId mappings
* Fix for subscription limits not being updated when adding/removing/transferring a database
* Fix for subscription limits not being updated when adding/removing a user
* Fix for BugSplat when navigating to Crash page with invalid ID

## Web App Update 05-October-2020

* Upgraded technical support editor and response pages. These now use Markdown (make that a link to the markdown website) rather than HTML. Existing HTML responses can be edited, but must be converted to Markdown. View an example in the fred@bugsplat.com database with this [link](https://app.bugsplat.com/browse/support/?stackKeyId=5555\&vendor=Fred\&key=\*Default\*).

## Web App Updates 30-Septeber-2020

* Fix bug displaying error message when GitHub defect creation fails

## Web App Updates 29-Septeber-2020

* Released new Support Response Editor
* Released new End User Support Response page

## Web Services API 29-September-2020

* Removed deprecated endpoint `crashRatePerMinute` from [Web Services API](../../../introduction/development/web-services/api/) documentation.

## Web App Updates 12-Septeber-2020

* Fix for bug where symbol upload disabled while files were uploading

## Web App Updates 04-Septeber-2020

* Fix for broken link in Pricing FAQs

## Web App Updates 02-Septeber-2020

* New Manual Symbol Upload control

## ngb-filterable-dropdown update 01-Septeber-2020

* Released version 3.0.0
* Added create item feature
* Updated style

## Android Update 1-August-2020

* BugSplat now supports collecting [Android NDK](../../../introduction/getting-started/integrations/mobile/android.md) crashes via Crashpad. If you're supporting a cross-platform C++ application, porting a C++ application to Android, or creating a new NDK library from scratch, check out our Android NDK docs and our AndroidCrasher sample application.

## Native, .NET, UE4, and SendPdbs downloads Update 24-July-2020

* Added support for a new '/Credentials' option in SendPdbs.
* Removed SendPdbs dependency on the external file PdbLibrary.dll
* SendPdbs updated to version 2.2.0.2.

## Web App Update 30-June-2020

* Released new version of the Stack Explorer page

## Web App Update 17-June-2020

* Added ability to search by version greater than or less than

## Web App Update 06-June-2020

* Added ability to export search results on Crashes page

## Web App Update 22-May-2020

* Release of [Qt](../../../introduction/getting-started/integrations/cross-platform/qt.md) Documentation

## Web App Update 20-May-2020

* Release of [CRYENGINE](../../../introduction/getting-started/integrations/game-development/cryengine.md) Documentation

## Web App Update 18-May-2020

* Site-wide style improvements

## Web App Update 15-May-2020

* Added crashing thread to Key Crash table row details view
* Added copy search feature
* Improved search UX
* Retired legacy search page
* Fix for 403 changing company logo

## Web App Update 14-May-2020

* Better error handling for long queries that have timed out

## Terms Update 12-May-2020

* Updated [Terms](../../../introduction/production/security-privacy-and-compliance/terms.md) of Service

## Web App Update 07-May-2020

* Added searches for any call stack file name, function name and line number
* Fix bug refreshing Crashes table after comment updated
* Fix bug loading crash page when application or version was undefined
* Style updates

## Web App Update 15-April-2020

* Released new Group Stacks page
* Improved Subkey UX
* Removed Heatmap page

## Web App Update 11-April-2020

* Support for filtering tables with query params
* Fixed bug where table headers didn't appear sortable
* Fixed bug where Symbol Details page settings weren't saved

## Web App Update 26-Mar-2020

* Added optional Environment field to Jira options

**Update for the weekend of 4-Apr-2020**

This weekend BugSplat is planning to upgrade our database to a new version. There may be short periods of time when the web application is non-responsive. Expect service to return to normal before Monday 6-April-2020.

We'll be posting updates here and on [Twitter](https://twitter.com/bugsplatco).

## Web App Update 17-Mar-2020

* Added support for [Discord](https://discordapp.com) notifications

## Web App Update 11-Mar-2020

* Hide horizontal scrollbar on row details views with no overflow
* Style improvements for restricted users on the ‘[Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)’ page.
* Anchored app footer to the bottom of the screen

## Web App Update 10-Mar-2020

* Added support for comma separated list of email addresses for email alerts

## Web App Update 06-Mar-2020

* Added support for comma separated list of email addresses for email alerts

## Web App Update 04-Mar-2020

* Added support for Microsoft Teams
* Removed deprecated Symbol Maintenance page

## Web App Update 23-Feb-2020

* Added crashing thread to expanded row detail view on Crashes page
* Hide ‘[Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)’ page for restricted users

## Web App Update 19-Feb-2020

* Moved transferring and deleting databases to
* Removed Database page

## Native SDK (Version 3.6.0.8) 18-Feb-2020

* Improvements in multi-thread crashing support

## Web App Updates 18-Feb-2020

* Add ability to disconnect Slack in Options - fixing features regress from release
* Improved UX setting support response logo

## Web App Updates 12-Feb-2020

* Added support for linking exist Azure DevOps issue with BugSplat
* Performance tweaks and improvements

## Web App Updates 10-Feb-2020

* Released new ‘[Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)’ page
* Improved log defect workflow

## Windows Native and .NET Update 06-Feb-2020

* **Updated to version 3.6.0.7**
  * Fixed bug in Windows Native SDK that could cause issues when simultaneous unhandled exceptions were raised from multiple threads. Updated BsSndRpt.exe to address issues on Windows 7 environments.
