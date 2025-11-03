# Full Memory Dumps

In some circumstances, the default memory dump image created by BugSplat may not contain enough information to diagnose the underlying problem leading to the crash. In these cases, it can be helpful to create a full memory dump. Note, this can be an expensive operation. The full memory dump can be really large, slow to upload, and slow to process.

BugSplat customers interested in processing full memory dumps should contact sales@bugsplat.com. There is a charge for this feature. After your account has been enabled for full memory dump processing, follow the instructions below.

#### Creating Full Memory Dumps for Windows Applications

To enable full memory dumps in your C++ application, call the setMiniDumpType method after initializing the BugSplat library:

```cpp
// BugSplat initialization  
BugSplat *g_BugSplat = new BugSplat("DbName", "AppName", "AppVersion");
g_BugSplat->SetMiniDumpType(MINIDUMP_TYPE::MiniDumpWithFullMemory);
```

**Creating Full Memory Dumps for .NET Apps**

To enable full memory dumps in your .NET application, set the MinidumpType to MiniDumpWithFullMemory after initializing BugSplat:

```csharp
// BugSplat initialization  
BugSplat.CrashReporter.Init(Database, App, Version);
BugSplat.CrashReporter.MinidumpType = 
                                BugSplat.MinidumpType.MiniDumpWithFullMemory;
 
```

**Collecting Full Memory Dumps**

After modifying your application, use the following steps to collect full memory dumps:

1. &#x20;Deploy your application. By default, crash reports will still be created with the normal minidump size.
2. Inside the BugSplat web application, the "Full Memory Dumps" checkbox on the [versions page](https://app.bugsplat.com/v2/versions) must be enabled to get a full memory crash dump. Enable the checkbox for each version of your application that should send full memory dumps. If the checkbox isn't set, application crashes will create normal minidump files. The Full Memory Dumps column is hidden by default; you must display it using the column visibility dropdown menu.
3. You should enable full memory dumps only for the duration required to capture your critical crash information. The overhead of the much larger full memory dump files will affect your customers' crash upload experience and the time needed to process the crash reports.
4. You can download crash reports containing full memory dumps using the crash details page's Attachments tab. Full memory dumps will typically not show the contents of the crash zip file. Your only option will be the "Download All" button.\
