# Full Memory Dumps

In rare circumstances, the default memory dump image created by BugSplat may not contain enough information to diagnose the underlying problem leading to the crash.  In these cases, it can be helpful to create a full memory dump.  Note, this can be an expensive operation.  The full memory dump can be really large, slow to upload, and slow to process. &#x20;

BugSplat customers interested in processing full memory dumps should contact sales@bugsplat.com.  There is a charge for this feature.  After your account has been enabled for full memory dump processing, follow the steps below.

#### Full Memory Dump Setup

\
(1) To enable full memory dumps in your application, code changes are required.  Call the setMiniDumpType method after initializing the BugSplat library:

```
// BugSplat initialization.  
mpSender = new MiniDmpSender("DbName", "AppName", "AppVersion" ... );
mpSender->setMiniDumpType( 
                MiniDmpSender::BS_MINIDUMP_TYPE::MiniDumpWithFullMemory ); 
```

\
(2) Deploy your application.  Note that by default, crash reports will still be created with the normal minidump size.

(3) Inside the BugSplat web application, the "full dump flag" on the [versions page](https://app.bugsplat.com/v2/versions) must be enabled in order to get a full memory crash dump.  Enable the flag for each version of your application that should send full memory dumps.  If that flag isn't set, a new crash will create a normal minidump file.  The Full Memory Dumps column is hidden by default, you'll need to display it using the column visibility dropdown menu. &#x20;

(4) You should enable full memory dumps only for the duration required to capture your critical crash information.  The overhead required to send and process full memory dumps will affect your customers' crash upload experience and the speed that BugSplat can process the crash reports.

(5) You can download crash reports containing full memory dumps using the crash details page Attachments tab.  Note that full memory dumps will typically not show the contents of the crash zip file.  Your only option will be the "Download All" button.\
\
