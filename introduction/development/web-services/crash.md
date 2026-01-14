# Crash Upload Endpoints

## Presigned URL Upload (Recommended)

The recommended method for uploading crash reports to BugSplat is via presigned URLs. This approach is more efficient and scalable than direct POST uploads. The process involves three steps:

1. Request a presigned upload URL from BugSplat
2. Upload your crash file and attachments directly to the presigned URL
3. Commit the upload to finalize processing

For a complete working example, see the [python-crash-upload](https://github.com/BugSplat-Git/python-crash-upload) repository.

{% hint style="info" %}
Before uploading, zip your crash file (e.g., minidump) and all attachments to reduce transfer size. The MD5 hash used in the commit step should be calculated from the zipped file.
{% endhint %}

### Step 1: Get Presigned Upload URL

<mark style="color:blue;">`GET`</mark> `https://{{database}}.bugsplat.com/api/getCrashUploadUrl`

Requests a presigned URL for uploading a zip file to BugSplat's storage.

#### Path Parameters

| Name                                             | Type   | Description                                                          |
| ------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | string | Replace the subdomain value with the value of your BugSplat database |

#### Query Parameters

| Name                                            | Type   | Description                              |
| ----------------------------------------------- | ------ | ---------------------------------------- |
| appName<mark style="color:red;">\*</mark>       | string | Name of the crashing application         |
| appVersion<mark style="color:red;">\*</mark>    | string | Version of the crashing application      |
| crashPostSize<mark style="color:red;">\*</mark> | number | Size of the zipped crash file in bytes   |

{% tabs %}
{% tab title="200: OK" %}
```json
{
    "url": "https://s3.amazonaws.com/bucket/path?presigned-params..."
}
```
{% endtab %}

{% tab title="429: Too Many Requests" %}
```json
{
    "error": "Too many requests"
}
```
{% endtab %}
{% endtabs %}

### Step 2: Upload to Presigned URL

<mark style="color:purple;">`PUT`</mark> `{{presigned_url}}`

Upload the zipped crash and attachments directly to the presigned URL returned from Step 1.

#### Headers

| Name                                             | Type   | Description                              |
| ------------------------------------------------ | ------ | ---------------------------------------- |
| Content-Type<mark style="color:red;">\*</mark>   | string | Must be `application/octet-stream`       |
| Content-Length<mark style="color:red;">\*</mark> | string | Size of the zipped crash file in bytes   |

#### Request Body

The raw binary content of your zipped crash file.

{% tabs %}
{% tab title="200: OK" %}
Empty response body indicates successful upload.
{% endtab %}
{% endtabs %}

### Step 3: Commit the Upload

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/api/commitS3CrashUpload`

Commits the uploaded crash file for processing by BugSplat.

#### Path Parameters

| Name                                             | Type   | Description                                                          |
| ------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | string | Replace the subdomain value with the value of your BugSplat database |

#### Request Body (multipart/form-data)

| Name                                          | Type   | Description                                                                           |
| --------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| appName<mark style="color:red;">\*</mark>     | string | Name of the crashing application                                                      |
| appVersion<mark style="color:red;">\*</mark>  | string | Version of the crashing application                                                   |
| crashType<mark style="color:red;">\*</mark>   | string | Type of crash (see Crash Type Reference below)                                        |
| crashTypeId<mark style="color:red;">\*</mark> | string | Numeric identifier for the crash type (see Crash Type Reference below)                |
| s3key<mark style="color:red;">\*</mark>       | string | The presigned URL returned from Step 1 (used as the S3 key reference)                 |
| md5<mark style="color:red;">\*</mark>         | string | MD5 hash of the zipped crash file for integrity verification                          |

{% tabs %}
{% tab title="200: OK" %}
```json
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

### Crash Type Reference

Use the following `crashType` and `crashTypeId` values when committing uploads:

| Platform              | crashType             | crashTypeId | Description                                      |
| --------------------- | --------------------- | ----------- | ------------------------------------------------ |
| Windows               | `Windows.Native`      | 1           | Native Windows minidumps (x86/x64)               |
| .NET                  | `DotNet`              | 2           | Legacy .NET crash reports                        |
| .NET                  | `DotNetDmp`           | 8           | .NET minidumps                                   |
| .NET Standard         | `DotNetStandard`      | 18          | .NET Core, UWP, and .NET Standard                |
| macOS                 | `PLCrashReporter`     | 13          | PLCrashReporter format                           |
| Crashpad/Breakpad     | `Crashpad`            | 6           | Cross-platform Crashpad/Breakpad minidumps       |
| Java                  | `Java`                | 4           | Java crash reports                               |
| JavaScript            | `JavaScript`          | 14          | Browser JavaScript errors                        |
| Angular               | `Angular`             | 19          | Angular framework errors                         |
| Node.js               | `NodeJs`              | 20          | Node.js crash reports                            |
| Electron              | `Electron`            | 22          | Electron application crashes                     |
| Python                | `Python`              | 23          | Python crash reports                             |
| Unity (Managed)       | `Unity`               | 12          | Unity managed/C# crash reports                   |
| Unity (Native)        | `UnityNative`         | 15          | Unity native crash dumps                         |
| Unreal Engine         | `UE4Native`           | 17          | UE4/UE5 Windows crash reports                    |
| Unreal Engine (Linux) | `UE4LinuxServer`      | 16          | UE4/UE5 Linux server crash reports               |
| Unreal Engine (Mac)   | `UE4Mac`              | 33          | UE4/UE5 macOS crash reports                      |
| iOS                   | `iOS`                 | 26          | iOS crash reports                                |
| PlayStation 4         | `PlayStation4`        | 28          | PS4 crash reports                                |
| PlayStation 5         | `PlayStation5`        | 29          | PS5 crash reports                                |
| Xbox                  | `Xbox`                | 27          | Xbox crash reports                               |
| Nintendo              | `Nintendo`            | 35          | Nintendo Switch crash reports                    |
| XML                   | `XmlReport`           | 21          | Custom XML-formatted crash reports               |

---

## Direct POST Endpoints

Most crash uploads to BugSplat are done via pre-signed URLs as described above. However, some platforms do not support uploading via pre-signed URLs. The following documentation describes how customers can POST crashes directly to BugSplat via a suite of endpoints specific to their BugSplat database.

{% hint style="warning" %}
&#x20;All these crashes must be uploaded via your BugSplat subdomain (e.g., https://your-database.bugsplat.com) to ensure that our backend accepts them.
{% endhint %}

## PlayStation 4

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/post/ps4/crash`

Uploads a Playstation 4 crash report, extracts user data and user files

#### Path Parameters

| Name                                             | Type   | Description                                                          |
| ------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | string | Replace the subdomain value with the value of your BugSplat database |

#### Request Body

| Name                                          | Type   | Description                                                                                                                             |
| --------------------------------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| corefile<mark style="color:red;">\*</mark>    | object | The core dump file to be uploaded                                                                                                       |
| application<mark style="color:red;">\*</mark> | string | <p>Name of the crashing application.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols.</p> |
| version<mark style="color:red;">\*</mark>     | string | <p>Crashing application's version.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols</p>    |

{% tabs %}
{% tab title="200 " %}
```
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

## PlayStation 5

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/post/ps5/crash`

Uploads a Playstation 5 crash report, extracts user data and user files

#### Path Parameters

| Name                                             | Type   | Description                                                          |
| ------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | string | Replace the subdomain value with the value of your BugSplat database |

#### Request Body

| Name                                          | Type   | Description                                                                                                                             |
| --------------------------------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| corefile<mark style="color:red;">\*</mark>    | object | The core dump file to be uploaded                                                                                                       |
| application<mark style="color:red;">\*</mark> | string | <p>Name of the crashing application.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols.</p> |
| version<mark style="color:red;">\*</mark>     | string | <p>Crashing application's version.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols</p>    |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

## Crashpad

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/post/bp/crash/crashpad.php`

Uploads a Crashpad crash report with optional metadata.

#### Path Parameters

| Name                                             | Type   | Description                                                         |
| ------------------------------------------------ | ------ | ------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | String | Replace the subdomain value with the name of your BugSplat database |

#### Request Body

| Name                                                     | Type   | Description                                                      |
| -------------------------------------------------------- | ------ | ---------------------------------------------------------------- |
| upload\_file\_minidump<mark style="color:red;">\*</mark> | FILE   | File POST parameter. This file can optionally be zip compressed. |
| other files                                              | FILE   | Any additional file POSTs will be attached to the crash report.  |
| product<mark style="color:red;">\*</mark>                | String | Application name                                                 |
| version<mark style="color:red;">\*</mark>                | String | Application version                                              |
| key                                                      | String | BugSplat crash key                                               |
| user                                                     | String | User reporting the crash                                         |
| list\_annotations                                        | String | User description of the problem                                  |
|                                                          | String |                                                                  |

{% tabs %}
{% tab title="200: OK " %}
```javascript
{
    "status": "success",
    "crash_id": 1,
    "url": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

## XML

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/post/xml/index.php`

Uploads an XML crash report and can be used to create reports for languages and platforms not directly supported by BugSplat.

#### Path Parameters

| Name                                             | Type   | Description                                                         |
| ------------------------------------------------ | ------ | ------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | String | Replace the subdomain value with the name of your BugSplat database |

#### Request Body

| Name                                         | Type   | Description                                                               |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------- |
| file<mark style="color:red;">\*</mark>       | FILE   | XML file containing crash report. File name must be **bsCrashReport.xml** |
| other files                                  | FILE   | Any additional file POSTs will be attached to the crash report.           |
| appName<mark style="color:red;">\*</mark>    | String | Application name                                                          |
| appVersion<mark style="color:red;">\*</mark> | String | Application version                                                       |
| appKey                                       | String | BugSplat crash key                                                        |
| user                                         | String | User reporting the crash                                                  |
| email                                        | String | Email of user                                                             |
| description                                  | String | User description of the problem                                           |
| ipAddress                                    | String | IP Address of machine generating report                                   |
| notes                                        | string | Arbitrary additional data about the crash report                          |

{% tabs %}
{% tab title="200: OK " %}
```javascript
{
    "status": "success",
    "crash_id": 1,
    "url": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

### Example XML File

```xml
<report>
    <platform>Windows</platform>
    <os>Windows 10 Version 20348 MP (4 procs) Free x86 compatible</os>
    <process>
        <exception>
            <code>FATAL ERROR</code>
            <explanation>This is an error code explanation</explanation>
            <func><![CDATA[myConsoleCrasher!MemoryException]]></func>
            <file>/www/bugsplatAutomation/myConsoleCrasher/myConsoleCrasher.cpp</file>
            <line>143</line>
            <registers>
                <cs>0023</cs>
                <ds>002b</ds>
                <eax>00000011</eax>
                <ebp>00affb58</ebp>
                <ebx>00858000</ebx>
                <ecx>43bf1e0e</ecx>
                <edi>00affb58</edi>
                <edx>014480b4</edx>
                <efl>00010202</efl>
            </registers>
        </exception>
        <modules numloaded="2">
            <module>
                <name>myConsoleCrasher</name>
                <order>1</order>
                <address>01320000-01457000</address>
                <path>C:/www/BugsplatAutomation/BugsplatAutomation/bin/x64/Release/temp/BugSplat/bin/myConsoleCrasher.exe</path>
                <symbolsloaded>deferred</symbolsloaded>
                <fileversion/>
                <productversion/>
                <checksum>00000000</checksum>
                <timedatestamp>SatJun1501:18:092019</timedatestamp>
            </module>
            <module>
                <name>BugSplatRc</name>
                <order>2</order>
                <address>01320000-01457000</address>
                <path>C:/www/BugsplatAutomation/BugsplatAutomation/bin/x64/Release/BugSplatRc.dll</path>
                <symbolsloaded>deferred</symbolsloaded>
                <fileversion/>
                <productversion/>
                <checksum>00000000</checksum>
                <timedatestamp>SatJun1501:18:092019</timedatestamp>
            </module>
        </modules>
        <threads count="2">
            <thread id="0" current="yes" event="yes" framecount="3">
                <frame>
                    <symbol><![CDATA[myConsoleCrasher!MemoryException]]></symbol>
                    <file>/www/bugsplatAutomation/myConsoleCrasher/myConsoleCrasher.cpp</file>
                    <line>143</line>
                    <offset>0x35</offset>
                </frame>
                <frame>
                    <symbol>
                        <![CDATA[myConsoleCrasher!wmain]]>
                    </symbol>
                    <file>C:/www/BugsplatAutomation/BugsplatAutomation/BugSplat/samples/myConsoleCrasher/myConsoleCrasher.cpp</file>
                    <line>83</line>
                    <offset>0x239</offset>
                </frame>
                <frame>
                    <symbol><![CDATA[myConsoleCrasher!__scrt_wide_environment_policy::initialize_environment]]></symbol>
                    <file>d:/agent/_work/4/s/src/vctools/crt/vcstartup/src/startup/exe_common.inl</file>
                    <line>90</line>
                    <offset>0x43</offset>
                </frame>
            </thread>
            <thread id="1" current="no" event="no" framecount="3">
                <frame>
                    <symbol><![CDATA[my2ConsoleCrasher!MemoryException]]></symbol>
                    <file>/www/bugsplatAutomation/myConsoleCrasher/myConsoleCrasher.cpp</file>
                    <line>143</line>
                    <offset>0x35</offset>
                </frame>
                <frame>
                    <symbol><![CDATA[my2ConsoleCrasher!wmain]]></symbol>
                    <file>C:/www/BugsplatAutomation/BugsplatAutomation/BugSplat/samples/myConsoleCrasher/myConsoleCrasher.cpp</file>
                    <line>83</line>
                    <offset>0x239</offset>
                </frame>
                <frame>
                    <symbol><![CDATA[my2ConsoleCrasher!__scrt_wide_environment_policy::initialize_environment]]></symbol>
                    <file>d:/agent/_work/4/s/src/vctools/crt/vcstartup/src/startup/exe_common.inl</file>
                    <line>90</line>
                    <offset>0x43</offset>
                </frame>
            </thread>
        </threads>
    </process>
</report>
```
