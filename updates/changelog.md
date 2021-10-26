# Changelog

List of recent product changes and updates for BugSplat. We announce these updates via [Twitter](https://twitter.com/bugsplatco).

## BugSplat Unity - Release - 2.1.0 - October 14th, 2021

* Adds support for configuring BugSplat via the Unity Editor
* Adds support for DontDestroyOnLoad

## BugSplat Unity - Release - 2.0.0 - October 7th, 2021

* Adds support for WebGL&#x20;
* Fixes a bug where PostMostRecentCrash posted the oldest crash

## Symbol Upload - Release - 3.0.0 - September 29th, 2021

Renames post and delete to postSymbols and deleteSymbols respectively

## JS API Client - Release - 2.3.3 - September 29th, 2021

* Fixes issue with TypeScript not generating correct paths in build output

## Symbol Upload - Release - 2.3.3 - September 29th, 2021

* Updates @bugsplat/js-api-client to a version compatible with Node.js

## JS API Client - Release - 1.0.0 - September 28th, 2021

* Adds client with support for posting crash reports
* Adds client with support for the OAuth2 Client Credentials flow

**Breaking Changes**

* Changes the order of the parameters for BugSplatApiClient's createAuthenticatedClientForNode and createAuthenticatedClientForWebBrowser functions so that host can be an optional parameter with a default value

## iOS - **Update - **September 26th, 2021

* Updated documentation.

## macOS - Release - 0.0.12 - September 24th, 2021

* Adds support for Crashes API

## JS API Client - Release - 0.0.12 - September 24th, 2021

* Adds support for Crashes API

## Web App Update - Release - 2.2.40 - September 22th, 2021

* Fixes typo in onboarding page

## Web App Update - Release - 2.2.39 - September 22th, 2021

* Fixes bug where some special characters were not allowed when creating new passwords

## Web App Update - Release - 2.2.38 - September 22th, 2021

* Update to dramatically increases the speed of Crash processing by reducing information displayed in Modules table

## Web App Update - Release - 2.2.37 - September 15th, 2021

* Updates links to docs

## Web App Update - Release - 2.2.36 - September 15th, 2021

* Improves Crash Rate Alert note
* Updates Sign Up page

## Web App Update - Release - 2.2.35 - September 3rd, 2021

* Increases password complexity requirements
* Adds Crash page link to docs for iOS crashes
* Updates Crash page links to new docs

## Web App Update - Release - 2.2.34 - August 25th, 2021

* Fixes text wrapping in row details component
* Adds a link to JavaScript docs for JavaScript crashes
* Adds support for uploading .js.map filesde.js.

## JS API Client -  **Breaking Changes** - Release 0.0.10 - August 25th, 2021

* Removes dependency on fs so that symbol upload can be done in a browser as well as via node.js.

## JS API Client -  v0.0.9 - August 25th, 2021

* Fixes bug where error was thrown if event username was undefined

## Web App Update - Release - 2.2.33 - August 17th, 2021

* Adds persistent column width to Crash page's Active Threads table

## Web App Update - Release - 2.2.31 - August 17th, 2021

* Fixes issue uploading .dSYM and .xcarchive packages with Safari
* Adds link to docs for ASAN crash reports
* Updates modal styles

## BugSplatNative library - v3.6.0.11. - August 5th, 2021

* Now supports Address Sanitizer crash reports. See [here](../introduction/getting-started/posting-a-test-crash/myconsolecrasher-c-plus-plus/address-sanitizer-reports.md) for details on this integration.

## BugSplatNative library - v3.6.0.10. - Jul 22nd, 2021

* MDSF\_NONINTERACTIVE option now also enforces silent reporting of application hangs.

## Web App Update - v2.2.30 - Jul 22nd, 2021

* Updates row details table on Versions page
* Updates row details table on Symbols page
* Updates row details table on Summary page
* Updates row details table on Company page
* Updates row details table on Dashboard page
* Updates Delete Symbols modal
* Updates Transfer Database modal
* Fixes padding for missing symbols warning
* Fixes padding for row details Active Thread table

## BugSplat Unity - v1.3.0 - Jul 20th, 2021

* Adds support for capturing UnityPlayer.log in UWP
* Fixes build error where PostBuild.cs could not find SendPdbs.exe in order to upload Plugin symbols
* Fixes TODO to destroy Texture2D after being used to create a screenshot

## Symbol Upload - v2.3.0 - Jun 29th, 2021

Adds support for removing symbols

## Remote Stack Analyzer - v2.0.0 - Jun 28th, 2021

* Fixes issues with corrupt crash report calculation
* Improved performance in MacOS and Crashpad crash processing
* Improved API error responses and error handling

## Web App Update - v2.2.28 - Jun 28th, 2021

* Updates Crash page styles

## Stack Converter - v1.2.3 - Jun 22nd, 2021

* Fixes broken bin command

## Stack Converter - v1.1.0 - Jun 22nd, 2021

* Fixes issue with invalid 'main' property in package.json

## BugSplat Unity - v1.2.0 - Jun 22nd, 2021

* Adds support for attaching Linux Editor.log and Player.log files

## Web App Update - v2.2.27 - Jun 20th, 2021

* Support for displaying defect events on Crash and Key Crash pages

## Web App Update - v2.2.26 - Jun 18th, 2021

* Adds markdown support to comments component
* Fixes BugSplat when attempting to set initials for comment component

## Web App Update - v0.0.6 - Jun 17th, 2021

* Fixes Events comment event comment property should be message

## Web App Update 16-June-2021

* Fixed issue where exported csv had duplicate columns
* Fixed issue where user couldn't login if database was deleted

## Web App Update 15-June-2021

* Fixed issue where database wasn't persisted when set by query param

## Web App Update 10-June-2021

* Added Reject Repeated Crashes option
* Fixed bug in Comment callout
* Fixed bug in ngx-charts where line chart was shaded incorrectly
* Replaced area charts with line charts

## Web App Update 04-June-2021

* Fixed issue with emails address containing a + being unable to login

## Web App Update 03-June-2021

* Added new Comment component and renamed old component
* Fixed incorrect sample crashes in grouped crashes table

## Web App Update 21-May-2021

* Fixed Key Crash breadcrumb overflow
* Fixed issue opening Stripe portal with popup blocker enabled

## Web App Update 17-May-2021

* Fixed Grouped crashes table sample crashes weren't filtered by time frame
* Fixed missing Key field on Crash page
* Added ability to specify crash type on Migrate page
* Added expand/collapse to long user descriptions on Crash page
* Added new environment icons on Crash page

## com.bugsplat.unity 1.1.0 Release 11-May-2021

* Adds support for attaching Player.log and Editor.log on macOS

## com.bugsplat.unity 1.0.0 Release 06-May-2021

* Official release of redesigned Unity package to GitHub and OpenUPM

## com.bugsplat.unity 0.0.2 Release 06-May-2021

* Beta release of redesigned Unity package to GitHub and OpenUPM

## BugSplatDotNetStandard 2.0.4 Release 04-May-2021

* Adds support for posting stringified exceptions

## BugSplatDotNetStandard 2.0.2 Release 29-April-2021

* Adds support for Unity exceptions

## BugSplatDotNetStandard 2.0.0 Release 29-April-2021

* Adds support for uploading minidumps
* Improves attachment support

## Temporary Outage Fixed 20-Apr-2020 11:00:00 am est

Due to an issue with our authentication service provider, BugSplat was temporarily unavailable for some users today. This issue has been resolved by the provider and is no longer an issue.

Check out [Twitter](https://twitter.com/bugsplatco) account for further updates.

## Web App Update 14-April-2021

* Fixed locals and arguments not shown for Unreal and Unity crashes
* Fixed filtering by eempty string
* Fixed broken platform image link to docs
* Added forward Unreal Engine crashes to Epic option

## Web App Update 08-April-2021

* Fixed broken Contact User button on Crash page
* Fixed bug navigating on Stack Explorer page
* Added links to filter crashes by email and user on Crashes page
* Crash page style updates

## Web App Update 05-April-2021

* Crash page style updates
* Fixed issue where arguments and locals where shown on unsupported platforms
* Fixed issue with missing placeholder in Safari date pickers

Web App Update 29-March-2021

* Fixed issue getting Crash data for February 28th
* Fixed issue getting Crash data for customers in timezones with 30 minute offsets
* Fixed formatting of Total Crashes field on Dashboard page

Web App Update 26-March-2021

* Fixed bug posting comment from Crash page
* Fixed bug creating/deleting defects from Crash page
* Fixed issue with icons not loading on Crash page
* Fixed broken Stack Key link on Crash page

## Web App Update 22-March-2021

* Fix bug with quotes in Error Log table
* Add warnings to Error Log page
* Add MFA status column to Company page
* Style changes

## Web App Update 08-March-2021

* Released new Onboarding flow

## Web App Update 02-March-2021

* Beta released Error Log page

## Web App Update 27-Feb-2021

* Added database selector to default Support Response page

## Web App Update 03-Feb-2021

* Updated Add User component to support adding multiple users at once

## Web App Update 01-Feb-2021

* Fix cursor pointer bug on Summary page
* Fix broken platform image for unknown platforms on Crash page

## Web App Update 28-Jan-2021

* Hide retired versions from applications/versions dropdowns

## Web App Update 19-Jan-2021

* Sort Application and Versions dropdown values
* Improved site accessibility

## Web App Update 13-Jan-2021

* Fix for error loading Symbols page when application name or version was null

## Web App Update 08-Jan-2021

* Fix bug displaying comments on Key Crash page

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

* Removed deprecated endpoint `crashRatePerMinute` from [Web Services API](../introduction/development/web-services/api.md) documentation.

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

* BugSplat now supports collecting [Android NDK](../introduction/getting-started/integrations/mobile/android.md) crashes via Crashpad. If you're supporting a cross-platform C++ application, porting a C++ application to Android, or creating a new NDK library from scratch, check out our Android NDK docs and our AndroidCrasher sample application.

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

* Release of [Qt](../introduction/getting-started/integrations/cross-platform/qt.md) Documentation

## Web App Update 20-May-2020

* Release of [CRYENGINE](../introduction/getting-started/integrations/game-development/cryengine.md) Documentation

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

* Updated [Terms](../introduction/production/security-privacy-and-compliance/terms.md) of Service

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
* Style improvements for restricted users on the Options page
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
* Hide Options page for restricted users

## Web App Update 19-Feb-2020

* Moved transferring and deleting databases to Options page
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

* Released new Options page
* Improved log defect workflow

## Windows Native and .NET Update 06-Feb-2020

* **Updated to version 3.6.0.7**
  * Fixed bug in Windows Native SDK that could cause issues when simultaneous unhandled exceptions were raised from multiple threads. Updated BsSndRpt.exe to address issues on Windows 7 environments.

## Web App Updates 31-Dec-2019

* Fixed bug where alerts appeared off-screen
* Fixed bug where table page could be set to a negative number

## Web App Updates 24-Dec-2019

* Published new Electron endpoint that does not require setting 'Include Electron Symbols' option
* Added platform indicator for Electron crashes on Crash page

## Web App Updates 03-Dec-2019

* Add First Report column to Summary page
* Fix bug where Key Crash table didn't honor global timeframe filter
* Fix more links broken by marketing site update

## Web App Updates 02-Dec-2019

* Updates to match new marketing site
* Added sign-up/team-access
* Added sign-up/crash-report

## Web App Updates 25-Nov-2019

* Added loading indicator for arguments and locals

## Web App Update 23-Nov-2019

* Fixed vertical line in charts on Dashboard, Summary and Key Crash pages in GMT timezone

## Web App Update 22-Nov-2019

* Fixes issue where safari users can't expand row details on crash page
* Fixes issue where crash info table scroll bars were always visible on crash page
* Hides symbol delete column in favor of a delete button in the expanded row view

## Web App Update 20-Nov-2019

* Fix bug where processing crash would spin forever

## Web App Update 16-Nov-2019

* Fix for errors generated by crashes where opening reprocess call returns 422

## Native SDK Updates 15-Nov-2019

* BugSplat native apps now support creation of full memory dumps. This option is only available to Enterprise customers. Contact us for more information.

## Web App Update 14-Nov-2019

* Performance improvements for processing crashes

## Web App Update 12-Nov-2019

* Fixed column sizing on Crash page
* Fix for Crash page row details being collapsed when additional info becomes available

## Web App Update 04-Nov-2019

* Fix bug where user cannot select Startup subscription

## Web App Update 30-October-2019

* Fix to display an error message when we can't reprocess a crash because it is too old
* Fix for active thread row details being closed for no reason on Crash page

## 25-October-2019: Web App Updates

* Fix BugSplat when create database clicked before API has time to respond
* Fix bug where KeyCrash comment could be set on wrong database
* Add subscription delete link to Account page
* Added commas to large numbers on various pages

## 23-October-2019: Web App Updates

* Added company selector to Company page
* Added users table to Company page
* Added ability to add user to all databases via Company page
* Added ability to remove user from all database via Company page
* Added ability to add database via Company page
* Added ability to delete database via Company page

## 9-October-2019: Web App Updates

* Fix bug where users table was not updated when restricted checkbox was toggled and user added
* Fix bug where comment field was not updated in Crashes and Key Crash page tables
* Fix bug where user was required to click away or hit enter to post Stack Key comment

## 16-September-2019: Web App Updates

* Disable monthly plan options if payment type is invoice
* Added 'Exception Message' and 'Exception Code' columns to KeyCrash page

## 13-September-2019: Web App Updates

* Added Exception Code and Exception Message columns to Crashes page
* Changed Symbol Details 'Size' column to 'Compressed Size'
* Fixed bug where Crashes page couldn't display crashes with undefined threads or modules
* Fixed bug displaying Crashes page 'User' value

## 11-Sept-2019: Versioning

* Windows version information added to BugSplat.dll and BsSndRpt.exe

## 09-September-2019: Web App Updates

* Style improvements to Account and Upgrade pages
* Added new indicator to the Account page for users who are paying by invoice
* Added new indicator to the Account page for users who are in the Free Tier plan
* Added new indicator to the Account page for users who are in the 30-Day Trial plan

## 05-September-2019: Account Tools Updates

* Added restricted menu for non-compliant accounts

## 23-August-2019: Web App and Tools Integration Updates

* Released integration with [GitHub Issues](../introduction/development/integrating-with-tools/issue-trackers/github-issues.md) issue tracker
* Released integration with [YouTrack](../introduction/development/integrating-with-tools/issue-trackers/youtrack.md) issue tracker
* Fixed bug where delinquent account could get around license lockout by navigating back
* Fixed bug where Crash page would not wait long enough for additional info
* Fixed bug where Crash page would not display modules for crash with 1 module
* Fixed bug on Crashes page where comment field wouldn't update when switching pages
* Improved mobile styles on Users page
* Improved styles on Crash page

## 22-August-2019: Web App and Docs Updates

* Release Versions page
* Updated FAQs on Account and Upgrade pages
* Display better error messages on Database page
* Display better error messages on Versions page

## 14-August-2019: Web App Updates

* Fix to show attachments even if crash has not finished processing

## 13-August-2019: Web App Updates

* Fix for bug where link to GroupStacks page had incorrect database parameter
* Fix for missing checkmark in Database page database selector
* Fix for error message when creating a new database

## 12-August-2019: Web App Updates

* Released Database page
* Updated /crash-details to /crash and added redirects
* Updated /company-details to /company and added redirects
* Updated /allcrash to /crashes and added redirects

## 09-August-2019: Web App Updates

* Beta release of the Database page

## 08-August-2019: Web App Updates

* Performance improvements for the Crash page
* Updated link on Company page to point to new Account page

## 31-July-2019: Web App Updates

* Added messaging for companies that have exceeded the limits of their plan
* Performance improvements

## 30-July-2019: Web App Updates

* Database selection error will now stay on the screen until user dismisses it

## 30-July-2019: Web App Updates

* Fixed bug where comment wouldn't be posted if user quickly navigated away from Crash Details page
* Added ability to download files from the Crash Details page while the crash is processing
* Added subscription countdown timer for trial users approaching the end of their trial
* Added link to symbols FAQ
* Error selecting a database will now stay on screen until the user dismisses it

## 10-July-2019: Web App Updates

* Change: Made post method awaitable
* Breaking Change: made logPostInfo and logError private
* Other: Fixed package vulnerabilities
* Updated to Angular 8
* Updated to TypeScript 3.5
* Fixed npm audit issues

## 30-July-2019: Web App Updates

* Fixed bug where comment wouldn't be posted if user quickly navigated away from Crash Details page
* Added ability to download files from the Crash Details page while the crash is processing
* Added subscription countdown timer for trial users approaching the end of their trial
* Added link to symbols FAQ
* Error selecting a database will now stay on screen until the user dismisses it

## 10-July-2019: Web App Updates

* Change: Made post method awaitable
* Breaking Change: made logPostInfo and logError private
* Other: Fixed package vulnerabilities
* Updated to Angular 8
* Updated to TypeScript 3.5
* Fixed npm audit issues

## 03-July-2019: Web App Updates

* Fixed bug where application filter did not work on Key Crash page

## 03-July-2019: Web App Updates

* Fixed bug where application filter did not work on Key Crash page

## 31-July-2019: Web App Updates

* Added messaging for companies that have exceeded the limits of their plan
* Performance improvements

## 30-July-2019: Web App Updates

* Database selection error will now stay on the screen until user dismisses it

## 30-July-2019: Web App Updates

* Fixed bug where comment wouldn't be posted if user quickly navigated away from Crash Details page
* Added ability to download files from the Crash Details page while the crash is processing
* Added subscription countdown timer for trial users approaching the end of their trial
* Added link to symbols FAQ
* Error selecting a database will now stay on screen until the user dismisses it

## 10-July-2019: Web App Updates

* Change: Made post method awaitable
* Breaking Change: made logPostInfo and logError private
* Other: Fixed package vulnerabilities
* Updated to Angular 8
* Updated to TypeScript 3.5
* Fixed npm audit issues

## 30-July-2019: Web App Updates

* Fixed bug where comment wouldn't be posted if user quickly navigated away from Crash Details page
* Added ability to download files from the Crash Details page while the crash is processing
* Added subscription countdown timer for trial users approaching the end of their trial
* Added link to symbols FAQ
* Error selecting a database will now stay on screen until the user dismisses it

## 10-July-2019: Web App Updates

* Change: Made post method awaitable
* Breaking Change: made logPostInfo and logError private
* Other: Fixed package vulnerabilities
* Updated to Angular 8
* Updated to TypeScript 3.5
* Fixed npm audit issues

## 03-July-2019: Web App Updates

* Fixed bug where application filter did not work on Key Crash page

## 03-July-2019: Web App Updates

* Fixed bug where application filter did not work on Key Crash page

## 27-June-2019: Web App Updates

* Released new Account page
* Released new Upgrade page

## 21-June-2019: Web App Updates

* Beta release of the Symbol Details page

## 20-June-2019: Web App Updates

* Persist column widths on All Crashes page between sessions
* Style improvements for Crash Details page

## 13-June-2019: Web App Updates

* Added key field to Crash Details page
* Location field is now the file name and line number of the crashing function on Crash Details page
* Combined code and explanation fields on Crash Details page
* Moved downloads to 'Attachments' tab on Crash Details page
* Fixed overflow scroll on crash info title cells on Crash Details page

## 06-June-2019: Web App Updates

Show database and user count as unlimited for Enterprise customers on Company Details page

## 31-May-2019: Web App Updates

Added database param to all pages for easier sharing of links

## 30-May-2019: Web App Updates

Fixed bug where users page could show users for wrong database

## 29-May-2019: Web App Updates

Fixed bug where database was undefined when navigating to the Key Crash page

## 28-May-2019: Web App Updates

Fixed bug where database was undefined when grouping stack on Crash Details page

## 25-May-2019: Web App Updates

* Fixed bug where database was undefined when creating defect from Key Crash page
* Fixed bug where database was undefined when creating defect from Crash Details page

## 24-May-2019: Web App Updates

* Added manage your plan link to company details
* Made Tech Support column invisible by default on Summary page

## 18-May-2019: Web App Updates

* Fixed BugSplat on Dashboard page when Crashes API was unreachable

## 25-May-2019: SendPdbs 2.2.0.0

* SendPdbs now uploads multiple symbol files in parallel to improve upload speed. The new version of SendPdbs can be downloaded separately and is also included in the updated versions of the Windows Native, Windows .NET, Unreal, Unity, and Crashpad SDKs.

## 17-May-2019: Web App Updates

* Released Crash Details page
* Added subscription enforcement dialog
* Added Stack Key ID column to Summary page
* Improved mobile layout

## 5-May-2019: Web App Updates

* Added 'Key' column to Key Crash page

## 1-May-2019: C++ SDK Updates

* VS2017 upgrade for C++ SDK. As of this release, our C++ SDK no longer supports Windows XP and Windows Vista.

## 30-April-2019: Web App Updates

* Fixed bug where Intercom didn't display for new users
* Fixed BugSplat when viewing Symbols page in Safari

## 25-April-2019: Web App Updates

* Added 'First Reported' and 'Last Reported' to Key Crash page
* Fixed column resize issue on all pages
* Set Defect Id and Stack Key Defect Id column defaults to hidden

## 24-April-2019: Web App Updates

* Reduced row height of all tables to display more data on page
* Changed '+ Add Filter' to '+ Column Search'
* Added database param to Key Crash links

## 19-April-2019: Web App Updates

* Added link to sub keyed stack key on Key Crash page
* Fixed 401 BugSplats

## 18-April-2019: Web App Updates

* Added sub key indicator to Key Crash page
* Added sub key indicator to Summary page
* Set 'Last Report' as default sort column on Summary page

## 16-April-2019: Released V2 Symbols Page

* Release of updated Symbols page in V2

## 8-April-2019: Web App Update

* Fixing loading of Intercom on V2 pages

## 8-April-2019 -macOS SDK Updates

* Ability to display a support response after submitting a crash.
* Ability to persist user's name and email so they are pre-filled next time the crash dialog appears.
* Ability to set a crash report expiry. When set the dialog will not show after the expiry time but the crash will still be uploaded.

## 5-April-2019: UWP / .NET Standard Beta Release

* Released BugSplatDotNetStandard version 1.0.0 on NuGet which supports all .NET 2.0 standard platforms. For information on how to integrate BugSplatDotNetStandard please see our [docs](../introduction/getting-started/integrations/cross-platform/dot-net-standard.md).

## 5-April-2019: BugSplat Website Update

* Company Details updates to support new subscription model
* Fix bug with handling of empty Symbols API response
* Crash Details updates

## 26-March-2019: BugSplat Website Update

* IE support
* Added Stack Key Defect Id indicator to Key Crash page
* Fixed bug where greater than filter was less than and vice versa

## 25-March-2019: BugSplat Website Update

* Fix conflicting style where filter hover was black instead of white
* Fix bug where user couldn't filter date columns
* Fix bug where IE wouldn't load due to missing padStart function
* Get nextCrashId, previousCrashId from API in Crash Details page
* Handle 401 in Crash Details component

## 16-March-2019: BugSplat Website Update

* Fix for defectId filtering
* Fix 401 BugSplat in DAV selector component
* Fix KeyCrash chart title overflow on mobile
* Truncate All Crash, Summary, Key Crash table overflow with ellipsis
* Truncate database selector overflow with ellipsis

## 08-March-2019: BugSplat Website Update

* Added mailto link to Email column on Key Crash page
* Added support for filtering Summary page by application and version
* Fixed BugSplat in time since last crash component

## 07-March-2019: Web Application Updates

* Fixed issue where chart data might be missing based on the current time and current timezone
* Fixed header padding issue on Company Details page
* Fixed spacing issue in Crash Details view

## 07-March-2019: Web Application Updates

* Fixed issue where chart data might be missing based on the current time and current timezone
* Fixed header padding issue on Company Details page
* Fixed spacing issue in Crash Details view

## 04-March-2019: Web Application Updates

* Fixed splat when viewing row details on KeyCrash page

## 03-March-2019: Web Application Updates

* Added ability to reset application/version/timeframe filter
* Fix for defectId column on KeyCrash page
* Fix for filter spacing
* Show 'Pending...' in recent crashes widget while a new stack key is processing
* Fix for missing 'Application Filter Enabled' indicator on Summary page

## 19-Feb-2019: Update to our Angular integration.

* bugsplat-ng updated to 1.0.0
* Fixes bug where rethrowErrors would attempt to recursively post to BugSplat
* Replaces rethrowError with an error log statement

## 15-Feb-2019: [Auth0](https://auth0.com) integration for login security

* As part of our continuing [security program](../introduction/production/security-privacy-and-compliance/security-program.md) at BugSplat, we’re switching to an authentication service called Auth0 for all of our account logins. Set via the [Password](https://app.bugsplat.com/v2/password/) page.
* Multi-Factor Authentication (MFA) for two-step login protocol requiring separate verification from a mobile device to access your account
* Federated logins via Google, Github, and Microsoft accounts

## 04-Feb-2019

* Fixed bug where email column cutoff on Users page (mobile)
* Fixed bug where id column was cut off on KeyCrash page (mobile)
* Fixed broken link on Login page
* Added mailto links to email addresses on Crashes page

## 30-Jan-2019

* Released new Users page

## 30-Jan-2019

* Fixed 403 error that happened when logging in as a different user
* Fixed crash that happened when invalid time frame was set in the date picker

## 26-Jan-2019

* Released new Key Crash page
* Released new Summary page (beta)
* Fixed bug where charts were the wrong color
* Added support for displaying times in local timezone

## 25-Jan-2019: Beta releases of KeyCrash and Users Page

* BugSplat has released beta versions of the KeyCrash page and the User page. This is available to our beta users. If you are interested in becoming a beta user, please email us at support@bugsplat.com.

## 15-Jan-2019: Time frame and Time Filter Update:

* Bug fix for page change resetting the Time Frame filter. Additionally, we released a feature where timezone information for crashes will now display on the Dashboard and Crashes pages.

## 09-Dec-2018: New Crashes Page Release

* BugSplat has released a rebuilt version of the Crash page. This release includes the launch of new features including:
* **Advanced column filtering options:** use the +Add Filter button to quickly find the crashes you're searching for based on an expanded field of inputs.
* **Customizable column visibility settings** easily show or hide columns to improve your workflow in BugSplat.
* **In-row crash details:** click the far left > to see information on any crash without having to navigate to the individual crash page.
* **Improved crash comment column:** quickly add comments to an individual crash from right on the All Crash page.

## 09-Nov-2018: Unity Native Version 1.2.0

* Added support for passing flags to BsSndRpt. The BugSplat.MSDF\_LOGCONSOLE flag will instruct BsSndRpt to not remove the dmp file when posting a crash report. Fixes bug that prevented some crash reports from being sent to BugSplat.

## 31-October-2018: SendPdbs 2.1.0.3

* This update forces SendPdb symbol uploads to use TLS1.2, which improves security during upload. The log output has also been updated to include timestamps and a new /verbose flag which may be useful in diagnosing slow uploads. The new version of SendPdbs can be downloaded separately and is also included in the updated versions of the Windows Native, Windows .NET, Unreal, Unity, and Crashpad SDKs.

## 19-Oct-2018: Planned Database Upgrade

* BugSplat will be upgrading our database on Saturday October 27th. The website will be operational during the upgrade, however, there will be a short period of time where new data will not be persisted in the updated database. If you notice any missing data from the morning of 27-Oct-2018 (MST) please contact support@bugsplat.com. Our support team is here to recover anything that is critical.

## 17-Sept-2018: BugsplatMac

* Released version [1.0.7](https://github.com/BugSplatGit/BugsplatMac/releases) of the BugsplatMac SDK. This release includes support for multiple file attachments.

## 15-September-2018: Planned Database Upgrade

* We experienced brief periods (1-2 minutes) of web app downtime during our database upgrade on Saturday September 15th. During these periods new crash reports may have been rejected by our servers.

## 30-August-2018: Intermittent Authentication Errors

* From August 28th through the morning of August 30th, BugSplat experienced intermittent errors connecting to our AWS DynamoDB database. These errors typically appeared under high load. As a result, some customers experienced failures during login or when using SendPdbs for automatic symbol uploads. A restart of our EC2 instances appears to have cleared the problem. Our investigation to determine the root cause of the issue continues. _Postmortem: It appears that configuration changes to a DynamoDB table invalidated some existing DynamoDB connections. A restart cleared the problem._

## 27-August-2018: Version 3.6.0.6

* This release fixes a bug in setGuardBytesBufferSize() in the Windows Native SDK. All customers using setGuardBytesBufferSize() should upgrade to this version.

## 14-August-2018: Unreal Upgrade

* Integrating Unreal with BugSplat is now performed by configuration changes in the Unreal environment. See our [Unreal docs](../introduction/getting-started/integrations/game-development/unreal-engine.md) for a full description. Our previous Unreal SDK package has been removed from the site.

## 07-August-2018: Electron Crash Analysis

* Electron spawns multiple processes and applications should call electron.crashReporter.start in each process to register a Crashpad/Breakpad handler with the appropriate metadata. In the past, failure to register all processes could result in crash reports that could not be analyzed. BugSplat now attempts to provide a good default when this information isn't supplied.

## 01-August-2018: Dashboard Release

* Dashboard replaces Overview as the in-app landing page. Includes improvements to charting, mobile responsiveness, and analytics.
* Compare crash rate across custom timeframes.
* Easily view total crash volume using custom timeframes.
* Drill down by application, database, and version.
* Compare crash rate to previous timeframes

## 30-July-2018: Version 3.6.0.5

* Added new method setGuardBytesBufferSize(). This can be used along with the MDSF\_USEGUARDMEMORY option to specify the size (in bytes) of the guard buffer. The default buffer size is 4 mb.

## 11-June-2018: Unity Native Version 1.1.0

* Added support for Unity 2018.1.

## 25-May-2018: Unreal Version 1.4.0

* Added the ability to specify a folder with additional files that will be attached to the crash report upload.

## 25-May-2018: Unity Native Version 1.0.1

* Fixed an issue where crashes that occurred in quick succession wouldn't post correctly. Fixed an issue locating crash files on systems when the username was changed and no longer matched the user profile directory.

## 25-May-2018: GDPR Compliance

* Published [document](../introduction/production/security-privacy-and-compliance/gdpr.md) outlining the steps we've taken to be GDPR compliant. It includes details on how to enter into a DPA with BugSplat. Further updates to our [Security](../introduction/production/security-privacy-and-compliance/) page as well as our [Privacy Policy](../introduction/production/security-privacy-and-compliance/privacy-policy.md).

## 02-May-2018: SendPdbs Version 2.1.0.0, BsSndRpt Version 3.6.0.2

* Upgrade of SendPdbs that improves symbol uploads and provides additional information on the Symbol Details page. This version also improves the Symbol Details page performance. Fixed an issue in BsSndRpt with '&' characters in the user description.

## 12-Apr-2018: Unity Cross-Platform Version 1.4.0

* Added ability to specify a callback that is executed after an error is posted to BugSplat via Reporter.SetCallback(Action callback). Fixed a warning about UnityWebRequest.Send being obsolete.

## 11-Apr-2018: bugsplat-js 3.0.0

* Released version [3.0.0](https://github.com/BugSplat-Git/bugsplat-js/releases) of bugsplat-js. This release adds a convenience function to automatically exit your application when it encounters a fatal error. Version 3.0.0 also adds the ability to specify an options object as well as a callback per invocation of bugsplat.post which is helpful for handling non-fatal errors. The full release notes for this version can be found [here](https://github.com/BugSplat-Git/bugsplat-js/releases/tag/v3.0.0).

## 26-Feb-2018: BugsplatMac 1.0

* Released version [1.0](https://github.com/BugSplatGit/BugsplatMac/releases) of BugsplatMac. This release includes support for attaching files to a crash report and for customizing the BugSplat dialog image.

## 22-Feb-2018: Website Updates

*
  * Manual symbol upload page improvements. Symbols now upload directly to Amazon S3 improving speed and reliability.
  * Breakpad and Mac symbols uploads are now supported on the manual upload page
  * "All" menu link renamed to "Crashes"
  * Pending crash reports on the Crashes page now have an active link that navigates you to the crash details page.
  * Common symbols libraries are now supported for all crash types. Previously only Windows symbols were supported.
  * Additional debugger output is now available for Mac and Breakpad crash reports. To view, select the "Calculate Details" and then the "Debugger Output" buttons for any crash report.

## 15-Feb-2018: Electron Automatic Symbols

* Symbols for Electron applications can now be automatically resolved. Select the new Include Electron Symbols checkbox on our [Options](https://app.bugsplat.com/v2/options) page to enable this feature.

## 29-Jan-2018: Unity Update

* We are happy to announce beta support for native Windows crashes in Unity and Unity Plugins! Check out our [docs](../introduction/getting-started/integrations/game-development/unity.md) page for more information regarding how to configure Unity native support.

## 22-Jan-2018: Website Update

* Changes to address security issues including improved protection against cross site forgery requests and issues with email addresses that map to existing database names.

## 20-Jan-2018: BugsplatMac Update

* Released version [0.9.20](https://github.com/BugSplatGit/BugsplatMac/releases) of BugsplatMac. This release includes a fix for an issue in archive uploads as well as updates to embedded libraries.

## 13-Jan-2018: Website Update

* Fixed a bug on our backend that was preventing macOS console application crashes from calculating correctly.

## 04-Jan-2018: Amazon ELB Outage

* Our web application experienced temporary connectivity issues when Amazon's Load Balancers in the US-EAST-1 region went down. The outage lasted a painful 13 minutes from 3:31:58 PM UTC-7 to 3:45:16 PM UTC-7. The issue has been resolved and our awesome crash reporting service is operating normally. Please feel free to send us a note if you have any questions or compliments.

## 20-Jan-2018: Website Update

* Old versions of SendPdbs have been retired as scheduled. If automated symbol uploads are not working for you, please upgrade to the latest version of SendPdbs (version 2.0.0.1) found [here](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs).

## 14-Nov-2017: Version 2.2.0

* Original error parameter from bugsplat.post is now passed to the callback method as the 3rd parameter (requestError, responseBody, originalError). Fixed a bug where callback wasn't invoked when there was an error posting to BugSplat.

## 09-Nov-2017: Website Update

* Rate limiting introduced for BugSplat website pages with the URL app.bugsplat.com. This rate limiting should not affect normal user navigation. However, customers integrating with our web services API should be prepared to handle HTML Error Code 429 - Too Many Requests.

## 07-Nov-2017: Version 1.3.1

* Added support for newline character (\r\n) in the UE4 CrashReportClient -Description parameter.

## 06-Nov-2017: Version 1.3.0

* Added ability to set the Key parameter via command line arguments.

## 06-Nov-2017: Version 3.6.0.0

* Default values for user, email and description now appear in crash dialog. Improved BsSndRpt usage dialog.

## 24-Oct-2017: Version 3.5.0.6

* Fixed a bug with large user descriptions which could cause crash uploads to fail.

## 19-Oct-2017: Version 2.1.0

* Added logic and tests to ensure that BugSplat raises helpful errors when called with invalid parameters.

## 18-Oct-2017: Version 2.0.3

* Added ability to optionally inject and control logging settings. Consumers of bugsplat-ng4 can now supply an instance of their own logger given it has the methods, error, warn, info and log. Consumers can also control the verbosity of log statements emitted by BugSplat.

## 08-Nov-2017: Version 1.3.0

* Added Unity 2017 support. Fixed bug where 2 dialogs were shown for prompted exceptions.

## 02-Oct-2017: Version 3.5.0.5

* Improvements to out-of-memory crash reporting. We now allocate a larger guard memory block and free it earlier in the unhandled exception handler code. All Native Windows applications should use the MDSF\_USEGUARDMEMORY flag to enable crash reports in out-of-memory situations. Also added back support for internal IP address reporting, which was dropped in version 3.4.0.2.

## 25-Oct-2017: Version 1.0.0.

* Replaced setCallback with observables/subscriptions. Added type for BugSplat API response data. Removed extraneous dependencies and improved download times dramatically. Build changes to target ES5.

## 17-Sept-2017: Version 0.0.3

* Performance improvements.

## 10-Sept-2017: Version 0.0.1

* Initial release of Angular ErrorHandler support

## 28-Aug-2017: Version 3.5.0.4

* Changed event log source name to include database and appName. This avoids potential conflicts when multiple applications are using BugSplat on the same computer.

## 02-July-2017: Version 3.5.0.3

* Fixed uptime calculation and added LaunchCount and LaunchToLastCrashTime entries to Windows native XML files.

## 20-June-2017: Version 1.2.0

* Added support for invoking CrashReportClient with command line parameters.

## 19-June-2017: Version 3.0.0

* Added support for UWP.

## 6/15/2017 - Version 3.5.0.2

* Added support for '+' characters in version numbers.

## 14-June-2017: Version 2.0.1

* Asynchronous uploading of additional files.

## 13-June-2017: Version 1.0.1

* Initial release of node.js error reporting.

## 08-June-2017: Version 3.5.0.1

* Fixed a bug that prevented the tech support response page from displaying in certain circumstances.

## 27-May-2017: Version 1.1.0

* Added support for paths containing spaces.

## 24-May-2017: Version 3.5.0.0

* Support for upload size limit per database. Added option for user to specify zip file path. Forced all network traffic to be on secure HTTPS.

## 19-May-2017: Version 3.4.0.3

* Added removeAdditionalFiles() method.

## 12-May-2017: Version 3.4.0.2

* Upload size restriction now enforced per database. Enterprise customers can get options to upload crash reports larger than 2mb. Crash reports now post directly to S3, for improved speed and reliability.

## 10-May-2017: Updates

* Added yjr BugSplatRc project to the .NET library in addition to the Native library where it has been for a long time. The resource-only DLL built by this project is used to define the crash dialog displayed to users by BsSndRpt.exe
* Updated BugSplatRc project name to BugSplatRc.vcxproj
* Settings for compiling EventLog.mc now work with paths containing spaces
* Release version changed to 3.4.0.1

## 28-April-2017: Version 1.0.0

* Initial release of BugSplat’s CrashReportClient.

## 21-April-2017: Version 3.4.0.0

* Converted to Unicode build. Dropped MBCS and C interfaces. Added setMinidumpType(), previously only available to a limited set of customers.

## Native SDK updates 21-April-2017

* Updated to Unicode build only.
* Dropped support for MBCS interfaces.
* Added support for setMinidumpType().
* Release version changed to 3.4.0.0

## Updates 26-Dec-2016

* Samples now use .NET 4.5. This allows us to support unwatchedExceptions. Added new UnwatchedException test in myDotNetCrasher.
* Updated BsSndRpt instructional text for manual uploads of crash reports which is displayed when there isn't a network connection. The previous links were displaying an out-of-date URL
* Samples now use .NET 4.5. This allows us to support unwatchedExceptions. Added new UnwatchedException test in myDotNetCrasher.

## Updates 22-Oct-2016

* Updated Windows zip downloads. References to bugsplatsoftware.com changed to bugsplat.com.

## Updates 01-Oct-2016

* BugSplat Native dll version 3.3.4.0
* Fix for 64-bit dynamic dll loading. 64-bit pointers were not being preserved.
* Native SDK zip and HTML documentation updated

## 27-Nov-2016

* Fixed incorrect link for manually posting crash reports.

## 07-Sept-2016

* Added support for setResourceDllPath().
* BugSplat native SDK version 3.3.3.0
* BugSplat logo update
* BsSndRpt support for Resource DLL path API function

## 26-July-2016 - Version 2.1.0

* Defect fixes and performance improvements.

## Updates 22-June-2016

* Fix for bug where typing too many characters into the text edit box prevents the crash report from being sent.

## Updates 03-March-2016

* SendPdbs update Usage text now refers to www.bugsplatsoftware.com/symbols
* Cleaned up error messages for improper usage.
* Updated version to 3.0.0.3

## Updates 03-March-2016

* Header file change to indicate MDSCB\_GETADDITIONALFILECOUNT is obsolete, not just deprecated

## Updates 29-December-2015

* Native SDK update
* BsSndRpt updated dialog text.
* Removed dbghelp.dll/lib from Native SDK
* Added sendAdditionalFile() API
* Added myConsoleCrasher sample which replaces bulky myCrasher sample
* Updated version to 3.3.2.0

## Updates 23-August-2015

* Updated dbghelp.dll to version 6.3.9600.17200
* Updated Java SDK. Fix for Java platform-dependent return value of System.getProperty("java.io.tmpdir"). On some platforms it is file-separator terminated, others not.
* Also deleted a stale duplicate version of BugSplat.java source.
* Updated .NET version to 1.1.03

## Updates 06-May-2015

* New doxygen manual page for BugSplatDotNet
* Fix for chinese characters in user-description

## Breakpad Updates 04-May-2015

* Added Breakpad support for Mac OS/X Yosemite
