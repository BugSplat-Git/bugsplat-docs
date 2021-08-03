# API

BugSplat provides RESTful web services to access data on our backend. The BugSplat database should be selected using the "database" URL parameter. Data is returned in JSON format. Authentication is required for all endpoints. See the curl example at the end of this page for an illustration of this process.

Want to see our API in action? Open your web browser inspector to see how our web application uses the endpoints below!

Most API endpoints support a "database={name}" parameter, used to specify which BugSplat database to use. In the absence of this parameter, the current \(default\) database will be selected.

## Web service API endpoints

* [https://app.bugsplat.com/api/allcrash](https://app.bugsplat.com/api/allcrash) GET - returns data for the Crashes page grid control. Supports [paging, filtering, and grouping](https://www.bugsplat.com/docs/api/paging-filtering-grouping) using column names: id, appName, appVersion, appKey, stackKeyId, stackKey, defectId, skDefectId, userDescription, user, email, exceptionCode, exceptionMessage, functionName, fileName, lineNumber, ipAddress, crashTime.
* [https://app.bugsplat.com/api/keycrash?stackKeyId=67](https://app.bugsplat.com/api/keycrash?stackKeyId=67) GET - returns data for the Key Crash page grid control. The stackKeyId must be specified using the stackKeyId parameter. Supports [paging, filtering, and grouping](https://www.bugsplat.com/docs/api/paging-filtering-grouping) using column names: id, appName, appVersion, appKey, stackKeyId, stackKey, defectId, skDefectId, userDescription, user, email, exceptionCode, exceptionMessage, functionName, fileName, lineNumber, ipAddress, crashTime.
* [https://app.bugsplat.com/api/company](https://app.bugsplat.com/api/company) GET - returns information about the company that owns the specified database PUT - update the specified companyName property associated with the specified company
* [https://app.bugsplat.com/api/crashHistory](https://app.bugsplat.com/api/crashHistory) GET - returns chartable data for specified database, appNames, appVersions, startDate and endDate
* [https://app.bugsplat.com/api/crash/data?id=400](https://app.bugsplat.com/api/crash/data?id=400) GET - returns call stack and associated metadata for crash id 400 as well as a processed property with the value 0 if the crash is still processing, value of 1 if the crashing thread has been processed and value of 2 if the crashing thread, background threads and debugger output have been processed.
* [https://app.bugsplat.com/api/databases](https://app.bugsplat.com/api/databases) GET - returns list of databases for the current user
* [https://app.bugsplat.com/api/logDefect?id=93](https://app.bugsplat.com/api/logDefect?id=93) GET - returns linked defect information for a given crash id or stackKeyId POST - creates a linked defect for a given crash id or stackKeyId or links an existing linkDefectId to a given crash id or stackKeyId if a defect tracker has been configured DELETE - removes the link to the defect for a given database id or stackKeyId
* [https://app.bugsplat.com/api/summary](https://app.bugsplat.com/api/summary) GET - returns data for the Summary page grid control. Supports [paging and filtering](https://www.bugsplat.com/docs/api/paging-filtering-grouping) using column names startDate, endDate, stackKey, stackKeyId, crashSum, userSum, subKeyDepth, defectId, comments, subject.
* [https://app.bugsplat.com/api/stackKeyComment?stackKeyId=49](https://app.bugsplat.com/api/stackKeyComment?stackKeyId=49) GET - returns comment for given database and stackKeyId PUT - sets comment for given database and stackKeyId
* [https://app.bugsplat.com/api/stackKeyDailyVolume?stackKeyIds\[\]=49](https://app.bugsplat.com/api/stackKeyDailyVolume?stackKeyIds[]=49) GET - returns chartable volumes for database and comma separated list of stackKeyIds
* [https://app.bugsplat.com/api/symbols](https://app.bugsplat.com/api/symbols) GET - returns information about symbol stores POST - creates a new symbol store
* [https://app.bugsplat.com/api/users](https://app.bugsplat.com/api/users) GET - returns a list of users and access rights for the specified database POST - adds a new user to the specified database DELETE - removes a user from the specified database if the caller is an unrestricted user in the specified database
* [https://app.bugsplat.com/api/user/userData?email=fred@bugsplat.com](https://app.bugsplat.com/api/user/userData?email=fred@bugsplat.com) GET - returns crash reports containing a specified email, username, or ipAddress. Use only one of the following URI terms: email={email}, username={username}, or ipAddress={ipAddress} and supply your own search term for the parts shown in curly brackets. DELETE - destroys the PII data associated with each matching crash report. This process will obfuscate the email, username, and ipAddress and delete the crash upload file.
* [https://app.bugsplat.com/api/versions](https://app.bugsplat.com/api/versions) GET - returns crash data grouped by application and version.
* [https://{database}.bugsplat.com/post/xml/](https://app.bugsplat.com/post/xml/) This is a POST-only endpoint that uploads a new xml crash report to an existing database. Replace {database} in the url with the name of your BugSplat database. The following parameters are supported:
  * appName - Required name of the application
  * appVersion - Required version of the application
  * bsCrashReport.xml - Required FILE POST parameter containing the crash report data. See the myConsoleCrasher example in the Windows C++ SDK for an example of the XML schema
  * appKey - Optional application key
  * user - Optional user name
  * email - Optional user email
  * description - Optional user description of crash event

### Example

You can access the web services with a variety of tools. Here’s an example using Curl to connect to the Fred database:

* rm cookies.txt
* curl -b cookies.txt -c cookies.txt --data "email=fred@bugsplat.com&password=Flintstone" https://app.bugsplat.com/api/authenticatev3
* curl -b cookies.txt -c cookies.txt "https://app.bugsplat.com/allCrash?database=Fred"
* curl -b cookies.txt -c cookies.txt "https://app.bugsplat.com/api/crash/data?id=58464&database=Fred"

## Special Rules for POSTing

When POSTing data to BugSplat endpoints additional steps are required to meet our Cross Site Request Forgery \(XSRF\) safety checks. After authenticating you will recieve a cookie named xsrf-token. Send that variable/value as a header parameter in all POST requests.

