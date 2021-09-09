# Localized Support Responses for Windows C++, .NET, and macOS

BugSplat's Support Response feature allows developers to display a localized message to their users at the time of a crash. The Support Response feature is currently supported by our [Windows C++](../../introduction/getting-started/integrations/desktop/cplusplus/), [.NET Framework](../../introduction/getting-started/integrations/desktop/windows-dot-net-framework.md), and [macOS](../../introduction/getting-started/integrations/desktop/macos.md) integrations and support for other platforms is coming soon. The following steps will allow you to create a localized Support Response message for all crashes in your database. These same steps can be applied to create localized [stack key specific Support Response](../../introduction/production/setting-up-custom-support-responses.md#creating-a-crash-specific-support-response) messages as well.

#### Step 1

Log in to the web application and navigate to the [Support Response](https://app.bugsplat.com/v2/support?stackKeyId=0&key=*Default*) page to edit the default support response for your database

#### Step 2

Create a new key for the localized version of your support message by typing a value in the key field. You'll want to remember what you typed here for later.

#### Step 3

Enter a subject for your support message. This subject is for internal use only and will not be displayed to the customer.

#### Step 4

In the code you use to initialize BugSplat, provide the value you used for key from step 2.

![Support Response Editor](../../.gitbook/assets/configure-custom-support-response-1.png)

**Windows C++**

```text
mpSender = new MiniDmpSender(L"Fred", L"myConsoleCrasher", L"1.0", L"es-ES", MDSF_USEGUARDMEMORY | MDSF_LOGFILE | MDSF_PREVENTHIJACKING);
```

**.NET**

```text
BugSplat.CrashReporter.Init("Fred", "myDotNetCrasher", "1.0");
BugSplat.CrashReporter.AppIdentifier = "es-ES";
```

**Mac OS**

```text
- (NSString *)applicationKeyForBugsplatStartupManager:(BugsplatStartupManager *)bugsplatStartupManager signal:(NSString *)signal exceptionName:(NSString *)exceptionName exceptionReason:(NSString *)exceptionReason;
{
    return @"es-ES";
}
```

#### **Step 5** 

Generate a crash and ensure that the correct support response is loaded, you should see your translated message.

![Support Response Page](../../.gitbook/assets/configure-custom-support-response-2.png)

