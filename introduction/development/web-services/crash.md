# Crash Post Endpoints

The following documentation describes how customers can POST crashes directly to BugSplat via a suite of endpoints specific to their BugSplat database. It is important that all these crashes are uploaded via your BugSplat subdomain to ensure that they are not rejected by our backend.

## Xbox

<mark style="color:green;">`POST`</mark> `https://{{database}}.bugsplat.com/post/xbox/crash`

Uploads an Xbox crash report and optional metadata

#### Path Parameters

| Name                                             | Type   | Description                                                          |
| ------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | string | Replace the subdomain value with the value of your BugSplat database |

#### Request Body

| Name                                         | Type   | Description                                                                                                                             |
| -------------------------------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| minidump<mark style="color:red;">\*</mark>   | object | The minidump file to be uploaded                                                                                                        |
| appName<mark style="color:red;">\*</mark>    | string | <p>Name of the crashing application.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols.</p> |
| appVersion<mark style="color:red;">\*</mark> | string | <p>Crashing application's version.</p><p><strong>IMPORTANT</strong></p><p>this value must match the value used to upload symbols</p>    |
| appKey                                       | string | Optional application identifier that provides extra data for searching and grouping                                                     |
| description                                  | string | Optional description of why the crash occurred                                                                                          |
| email                                        | string | Optional email address for the user that crashed                                                                                        |
| ipAddress                                    | string | Optional IP address of the crashing user                                                                                                |
| user                                         | string | Optional username for the user that crashed                                                                                             |

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
```
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endtab %}
{% endtabs %}

## Crashpad

<mark style="color:green;">`POST`</mark> `{{database}}.bugsplat.com/post/bp/crash/crashpad.php`

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

### Example

The following is an example that uses curl to demonstrate posting a crash to BugSplat. Be sure to update the value of `{{database}}` with the value of your BugSplat database.

```
curl --request POST 'https://{{database}}.bugsplat.com/post/xbox/crash' \
--form 'appName="my-xbox-crasher"' \
--form 'appVersion="1.0.0"' \
--form 'minidump=@"/path/to/minidump.dmp"'
```

## XML

<mark style="color:green;">`POST`</mark> `{{database}}.bugsplat.com/post/xml/index.php`

Uploads an XML crash report and can be used to create reports for languages and platforms not directly supported by BugSplat.

#### Path Parameters

| Name                                             | Type   | Description                                                         |
| ------------------------------------------------ | ------ | ------------------------------------------------------------------- |
| \{{database\}}<mark style="color:red;">\*</mark> | String | Replace the subdomain value with the name of your BugSplat database |

#### Request Body

| Name                                         | Type   | Description                                                      |
| -------------------------------------------- | ------ | ---------------------------------------------------------------- |
| file<mark style="color:red;">\*</mark>       | FILE   | File POST parameter. This file can optionally be zip compressed. |
| other files                                  | FILE   | Any additional file POSTs will be attached to the crash report.  |
| appName<mark style="color:red;">\*</mark>    | String | Application name                                                 |
| appVersion<mark style="color:red;">\*</mark> | String | Application version                                              |
| appKey                                       | String | BugSplat crash key                                               |
| user                                         | String | User reporting the crash                                         |
| email                                        | String | Email of user                                                    |
| description                                  | String | User description of the problem                                  |
| ipAddress                                    | String | IP Address of machine generating report                          |
| notes                                        | string | Arbitrary additional data about the crash report                 |

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
