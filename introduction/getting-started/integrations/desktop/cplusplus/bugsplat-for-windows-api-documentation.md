# BugSplat Native API Documentation

***

### Class: BugSplat

#### Constructor

```cpp
BugSplat(const wchar_t* database, 
         const wchar_t* appName, 
         const wchar_t* appVersion, 
         LPTOP_LEVEL_EXCEPTION_FILTER lpTopLevelExceptionFilter = nullptr);
```

**Description:** Initializes a new BugSplat instance for crash reporting.

**Parameters:**

* `database` - The BugSplat database identifier
* `appName` - Name of your application
* `appVersion` - Version string of your application
* `lpTopLevelExceptionFilter` - Optional custom top-level exception filter (default: nullptr)

#### Destructor

```cpp
~BugSplat();
```

**Description:** Cleans up BugSplat resources and restores original exception handlers.

***

### Configuration Methods

#### SetQuietMode

```cpp
void SetQuietMode(bool flag);
```

**Description:** Controls whether the crash report dialog is presented to the user (desktop applications only).  QuietMode is off by default.

**Parameters:**

* `flag` - `true` to suppress the dialog, `false` to show it

#### SetKey

```cpp
void SetKey(const wchar_t* key);
```

**Description:** Sets the crash 'key' field for crash identification and grouping.

**Parameters:**

* `key` - Unique identifier string for this crash context

#### SetUser

```cpp
void SetUser(const wchar_t* user);
```

**Description:** Sets the default 'user' field. The crash dialog may allow users to override this value.

**Parameters:**

* `user` - Username or user identifier

#### SetEmail

```cpp
void SetEmail(const wchar_t* email);
```

**Description:** Sets the default 'email' field. The crash dialog may allow users to override this value.

**Parameters:**

* `email` - User's email address

#### SetUserDescription

```cpp
void SetUserDescription(const wchar_t* description);
```

**Description:** Sets the default 'userDescription' field. The crash dialog may allow users to override this value.

**Parameters:**

* `description` - User-provided description of what happened before the crash

#### SetNotes

```cpp
void SetNotes(const wchar_t* notes);
```

**Description:** Sets the initial value of the 'notes' field. BugSplat web application users can edit this field.

**Parameters:**

* `notes` - Additional notes or debugging information

#### SetAttribute

```cpp
void SetAttribute(const wchar_t* name, const wchar_t* value);
```

**Description:** Sets custom attributes that will be included with crash reports.

**Parameters:**

* `name` - Attribute name
* `value` - Attribute value

#### SetMiniDumpType

```cpp
void SetMiniDumpType(MINIDUMP_TYPE dumpType);
```

**Description:** Configures the type of minidump to generate during crashes.

**Parameters:**

* `dumpType` - Windows MINIDUMP\_TYPE enumeration value

#### SetHangDetectionTimeout

```cpp
void SetHangDetectionTimeout(int ms);
```

**Description:** Sets the timeout used to determine if a process is hung.

**Parameters:**

* `ms` - Timeout in milliseconds (default: 5000). Use 0 to disable hang detection.

#### SetCrashCompletionBehavior

```cpp
enum class BugSplatCrashCompletion { Exit = 0, Terminate = 1, ContinueSearch = 2 };

void SetCrashCompletionBehavior(BugSplatCrashCompletion behavior);
```

**Description:** Controls what the crash handler does after the crash report has been created and uploaded. The dump is captured and sent before any of these paths, so reporting is unaffected by the choice:

* `Exit` (default) - calls `exit()`, which runs full C-runtime shutdown.
* `Terminate` - calls `TerminateProcess`, a hard termination that avoids CRT-shutdown hangs in complex hosts (for example, a Unity standalone player whose process can hang on `exit()` after the report is sent).
* `ContinueSearch` - returns `EXCEPTION_CONTINUE_SEARCH` from the unhandled-exception filter, handing control to the operating system's default unhandled-exception handling (Windows Error Reporting, or an attached debugger) instead of ending the process itself.

**Parameters:**

* `behavior` - one of the `BugSplatCrashCompletion` values above (default `Exit`)

#### SetCrashType

```cpp
void SetCrashType(int crashTypeId);
```

**Description:** Overrides the BugSplat crash type id stamped on uploaded crashes. By default native crashes are uploaded as `Native` (id `1`). Set this when a higher-level integration needs the server to process the crash differently. For example, a Unity IL2CPP integration sets `15` (`UnityNative`: "a native crash with an additional file containing the managed call stack"), which is the crash type the BugSplat backend uses to apply `LineNumberMappings.json` and symbolicate managed (C#) frames.

**Parameters:**

* `crashTypeId` - the BugSplat crash type id (default `1` = Native; `15` = UnityNative)

***

### Crash Detection & Reporting

#### GenerateDump

```cpp
void GenerateDump(LPEXCEPTION_POINTERS const exceptionPointers, 
                  MINIDUMP_TYPE dumpType = (MINIDUMP_TYPE)(MiniDumpNormal|MiniDumpFilterTriage)) const;
```

**Description:** Manually generates a BugSplat crash report with the specified exception information.

**Parameters:**

* `exceptionPointers` - Pointer to exception information structure
* `dumpType` - Type of minidump to create (default: MiniDumpNormal|MiniDumpFilterTriage)

#### CreateXmlReport

```cpp
void CreateXmlReport(const wchar_t* xmlReport);
```

**Description:** Sends an XML report to BugSplat, bypassing minidump creation. Program execution continues normally after this call.

**Parameters:**

* `xmlReport` - XML-formatted report string

**Note:** See MyConsoleCrasher.cpp for XML schema examples.

#### CreateAsanReport

```cpp
void CreateAsanReport(const char* asanReport);
```

**Description:** Creates a crash report specifically for AddressSanitizer (ASAN) errors.

**Parameters:**

* `asanReport` - ASAN error report string

***

### File Attachments

#### AddAttachment

```cpp
bool AddAttachment(const wchar_t* filepath);
```

**Description:** Adds a file to be included with crash reports and feedback uploads.

**Parameters:**

* `filepath` - Full path to the file to attach

**Returns:** `true` if the file was successfully added, `false` otherwise

#### RemoveAttachment

```cpp
bool RemoveAttachment(const wchar_t* filepath);
```

**Description:** Removes a single file attachment from the attachment list.

**Parameters:**

* `filepath` - Full path to the file to remove

**Returns:** `true` if the file was found and removed, `false` otherwise

#### ClearAttachments

```cpp
void ClearAttachments();
```

**Description:** Removes all previously added file attachments.

***

### User Feedback

#### PostFeedback

```cpp
bool PostFeedback(const wchar_t* title,
                  const wchar_t* description = L"",
                  const std::vector<const wchar_t*>& attachments = {});
```

**Description:** Posts non-crashing user feedback such as bug reports or feature requests. Feedback reports appear in BugSplat with the "User Feedback" type, grouped by title.

**Parameters:**

* `title` - Feedback title, used as the stack key for grouping
* `description` - Optional description of the feedback (default: empty string)
* `attachments` - Optional list of file paths to include with the feedback (default: empty)

**Returns:** `true` if the feedback was posted successfully, `false` otherwise

**Note:** Attachments passed via the `attachments` parameter are automatically removed after upload. Attachments added via `AddAttachment()` are not affected.

#### PostFeedbackWithResult

```cpp
struct FeedbackResult {
    bool success = false;
    int crashId = 0;
    std::wstring infoUrl;
};

FeedbackResult PostFeedbackWithResult(const wchar_t* title,
                                      const wchar_t* description = L"",
                                      const std::vector<const wchar_t*>& attachments = {});
```

**Description:** Posts user feedback like `PostFeedback`, and also returns the id BugSplat gave the report and its URL, so your feedback UI can show the report id or link to it. `PostFeedback` calls this function and returns only `success`.

The call waits until the upload finishes, so call it from a background thread rather than your UI thread. Hang detection is paused while it runs.

**Parameters:**

* `title` - Feedback title, used as the stack key for grouping
* `description` - Optional description of the feedback (default: empty string)
* `attachments` - Optional list of file paths to include with this feedback only (default: empty)

**Returns:** A `FeedbackResult`:

* `success` - `true` if the feedback was posted
* `crashId` - The BugSplat report id of the feedback
* `infoUrl` - The URL BugSplat returned for the report

On failure, `success` is `false`, `crashId` is `0`, and `infoUrl` is empty.

**Note:** Added in version 8.0.0.

***

### Crash Management

#### PostCrash

```cpp
bool PostCrash();
```

**Description:** Posts a single crash report and removes the folder after successful upload.

**Returns:** `false` if no BugSplat monitor is available

#### PostAllCrashes

```cpp
bool PostAllCrashes();
```

**Description:** Posts all pending crash reports. This method blocks until completion.

**Returns:** `true` once the monitor has finished posting, whether or not there were crashes to post. `false` if no BugSplat monitor is available.

**Note:** Should only be called on a new thread to avoid blocking the main application.

#### PostAllCrashesAsync

```cpp
bool PostAllCrashesAsync();
```

**Description:** Posts all pending crash reports on a new background thread.

**Returns:** Always returns `true`

#### AttachToProcess

```cpp
bool AttachToProcess(DWORD pid);
```

**Description:** For use in a custom Windows Error Reporting (WER) runtime exception module. WER runs the module inside `WerFault.exe`, where a `BugSplat` instance has no monitor of its own. `AttachToProcess` connects the instance to the monitor of the crashed application, so `PostCrash` uploads through that monitor. Most applications don't need this: registering `BugSplatWer.dll` reports fail-fast crashes without any code (see [Registry Changes](bugsplat-for-windows-upgrade-guide.md#registry-changes)).

Get the crashed application's process id from the `hProcess` that WER passes to your module. After a successful attach, write your minidump and any extra files into `GetCrashFolder()`, then call `PostCrash`. Crash fields you set on the instance, such as `SetUserDescription`, are included in the report, along with the files the crashed application registered with `AddAttachment`. The crash dialog follows the crashed application's `SetQuietMode` setting.

```cpp
DWORD crashedPid = GetProcessId(pExceptionInformation->hProcess);
if (g_BugSplat.AttachToProcess(crashedPid))
{
    // Write your minidump, plus any extra files to attach, into g_BugSplat.GetCrashFolder().
    g_BugSplat.PostCrash();
}
```

**Parameters:**

* `pid` - Process id of the crashed application

**Returns:** `true` if the instance is attached. `false` if `pid` is `0` or the current process, or if that process has no running BugSplat monitor this SDK can talk to. If it returns `false`, decline the crash in your module so WER can pass it to its other handlers.

**Notes:**

* The crashed application must create its own `BugSplat` instance with the same SDK release as your module.
* Attaching stops the monitor that your instance started. If the attach fails, `PostCrash` and the other upload calls return `false` instead of waiting for a monitor.
* Don't also list `BugSplatWer.dll` under `RuntimeExceptionHelperModules`. WER gives each crash to whichever registered module claims it.
* Added in version 8.2.0.

***

### Utility Methods

#### IsWerEnabled

```cpp
bool IsWerEnabled();
```

**Description:** Checks if Windows Error Reporting integration is currently enabled.

**Returns:** `true` if WER integration is enabled, `false` otherwise

#### AllocGuardMemory

```cpp
void AllocGuardMemory(size_t nbytes);
```

**Description:** Allocates guard memory that is freed in the default GlobalExceptionFilter.  By default, a three-megabyte guard memory block is created.&#x20;

**Parameters:**

* `nbytes` - Number of bytes to allocate

#### FreeGuardMemory

```cpp
void FreeGuardMemory();
```

**Description:** Frees previously allocated guard memory.

#### GetCrashFolder

```cpp
const wchar_t* GetCrashFolder();
```

**Description:** Returns the folder path where current crash artifacts will be stored. BugSplat moves to a new folder after each upload, so copy the string rather than keeping the pointer.

**Returns:** Path string in format `BugSplat\{appName}-{appVersion}\{unique-guid-string}` under the user's temp folder on desktop, or under persistent local storage on Xbox

#### SetSuspendingState

```cpp
void SetSuspendingState(BOOL status);
```

**Description:** Tells BugSplat that the application is suspending, for example across system sleep, or has resumed. While it's suspending, the monitor skips hang detection and crash reports are not generated.

**Parameters:**

* `status` - `TRUE` while the application is suspending, `FALSE` on resume

#### GetLogFilePath

```cpp
const wchar_t* GetLogFilePath();
```

**Description:** Returns the path to the BugSplat log file.

**Returns:** Path string to log file

#### CleanupExceptionSystem

```cpp
void CleanupExceptionSystem();
```

**Description:** Explicitly remove BugSplat's current working folder, log file, and crash state file. This function is needed on Xbox because the system terminates the monitor program and doesn't give it a chance to exit.

***

### Helper Functions

#### CRT Exception Handling

BugSplat provides helper functions to configure CRT (C Runtime) exception handling for comprehensive crash detection.

**SetGlobalCRTExceptionBehavior**

```cpp
inline void SetGlobalCRTExceptionBehavior();
```

**Description:** Configures global CRT exception handlers. Should be called once during application initialization.

**Configured Handlers:**

* `set_terminate()` - Handles C++ termination
* `_set_purecall_handler()` - Handles pure virtual function calls
* `_set_invalid_parameter_handler()` - Handles invalid parameter errors
* `_set_new_handler()` - Handles memory allocation failures

**SetPerThreadCRTExceptionBehavior**

```cpp
inline void SetPerThreadCRTExceptionBehavior();
```

**Description:** Configures per-thread CRT exception handling. It should be called in each thread of your application.

**Configured Handlers:**

* Signal handling for SIGABRT
* Abort behavior configuration

***

### C API (BugSplatC.h)

In addition to the `BugSplat` C++ class, the SDK exposes a flat C API declared in **`BugSplatC.h`**. The C API manages a single process-wide `BugSplat` instance and is the interface exported by the dynamic library, **`BugSplat.dll`**. Because only the C ABI crosses the DLL boundary, consumers of `BugSplat.dll` do not need to match the SDK's runtime library setting (`/MT` vs `/MD`), and any language with C FFI support (C#, Rust, Python, etc.) can call these functions directly.

**Linkage:**

* **Dynamic:** link the import library `lib\dll\BugSplat.lib` and ship `BugSplat.dll` with your application. This is the default when including `BugSplatC.h` with no extra defines.
* **Static:** the C API is also compiled into both static flavors of `BugSplat.lib` (`lib\md` for `/MD` builds, `lib\mt` for `/MT` builds). Define `BUGSPLAT_STATIC` before including `BugSplatC.h`.

All strings are null-terminated UTF-16 (`wchar_t*`). Boolean parameters and return values use `int` (`0`/`1`) for ABI stability.

#### BugSplat\_Init

```c
int BugSplat_Init(const wchar_t* database, const wchar_t* appName, const wchar_t* appVersion);
```

**Description:** Initializes crash reporting and installs the unhandled exception filter. Call once, early in your application's lifetime.

**Returns:** `1` on success, `0` if already initialized or if any argument is null.

#### BugSplat\_IsInitialized

```c
int BugSplat_IsInitialized(void);
```

**Description:** Returns `1` if `BugSplat_Init` has been called successfully, `0` otherwise.

#### BugSplat\_IsWerEnabled

```c
int BugSplat_IsWerEnabled(void);
```

**Description:** Returns `1` if BugSplat's Windows Error Reporting runtime exception module is registered for this process, `0` otherwise. Fail-fast terminations — stack buffer overrun (`0xC0000409`), heap corruption (`0xC0000374`), and `__fastfail` — bypass the unhandled exception filter, so they are reported only when this returns `1`.

Registration requires `BugSplatWer.dll` beside the host executable and a value naming its full path under `RuntimeExceptionHelperModules` (see [Registry Changes](bugsplat-for-windows-upgrade-guide.md#registry-changes)). It fails silently when either is missing, so check this at startup and warn during development.

**Returns:** `1` if WER integration is registered, `0` otherwise.

#### Forwarding Functions

The remaining functions forward to the equivalent `BugSplat` class methods documented above:

| C function                          | Class method               |
| ----------------------------------- | -------------------------- |
| `BugSplat_SetKey`                   | `SetKey`                   |
| `BugSplat_SetUser`                  | `SetUser`                  |
| `BugSplat_SetEmail`                 | `SetEmail`                 |
| `BugSplat_SetUserDescription`       | `SetUserDescription`       |
| `BugSplat_SetNotes`                 | `SetNotes`                 |
| `BugSplat_SetAttribute`             | `SetAttribute`             |
| `BugSplat_AddAttachment`            | `AddAttachment`            |
| `BugSplat_RemoveAttachment`         | `RemoveAttachment`         |
| `BugSplat_SetQuietMode`             | `SetQuietMode`             |
| `BugSplat_SetHangDetectionTimeout`  | `SetHangDetectionTimeout`  |
| `BugSplat_SetCrashCompletionBehavior` | `SetCrashCompletionBehavior` |
| `BugSplat_SetCrashType`               | `SetCrashType`               |
| `BugSplat_PostAllCrashesAsync`      | `PostAllCrashesAsync`      |
| `BugSplat_CreateXmlReport`          | `CreateXmlReport`          |
| `BugSplat_CreateAsanReport`         | `CreateAsanReport`         |
| `BugSplat_SetMiniDumpType`          | `SetMiniDumpType`          |

`BugSplat_SetMiniDumpType` takes the Windows `MINIDUMP_TYPE` flags as an `int`. `BugSplat_CreateAsanReport` takes a `const char*` (ASan reports are ASCII/UTF-8 text).

#### BugSplat\_PostFeedback

```c
int BugSplat_PostFeedback(const wchar_t* title,
                          const wchar_t* description,
                          const wchar_t* const* attachments,
                          int attachmentCount);
```

**Description:** Posts non-crashing user feedback such as a bug report or feature request. The title is used as the stack key for grouping feedback in the dashboard.

**Parameters:**

* `title` - Feedback title (also the grouping key)
* `description` - Optional description; may be `NULL` (treated as empty)
* `attachments` - Optional array of file paths, or `NULL` for none. These are included only in this feedback upload and do not affect attachments added via `BugSplat_AddAttachment`
* `attachmentCount` - Number of entries in `attachments` (0 if none)

**Returns:** `1` on success, `0` on failure.

**Note:** The C++ `BugSplat::PostFeedback` takes a `std::vector`, which cannot cross the DLL boundary. The C entry point takes an `(array, count)` pair instead so the C ABI stays free of STL types and remains compatible with `/MT` and non-C++ consumers.

#### BugSplat\_PostFeedbackWithResult

```c
int BugSplat_PostFeedbackWithResult(const wchar_t* title,
                                    const wchar_t* description,
                                    const wchar_t* const* attachments,
                                    int attachmentCount,
                                    int* outCrashId,
                                    wchar_t* outInfoUrl,
                                    int outInfoUrlChars);
```

**Description:** Posts user feedback like `BugSplat_PostFeedback`, and also returns the id BugSplat gave the report and its URL, so your feedback UI can show the report id or link to it. The C counterpart of `PostFeedbackWithResult`. The call waits until the upload finishes, so call it from a background thread rather than your UI thread.

**Parameters:**

* `title`, `description`, `attachments`, `attachmentCount` - As for `BugSplat_PostFeedback`
* `outCrashId` - Receives the BugSplat report id. May be `NULL` if you don't need it.
* `outInfoUrl` - Buffer that receives the URL BugSplat returned for the report, or `NULL` to skip it. The URL is truncated if it doesn't fit and is always null-terminated.
* `outInfoUrlChars` - Capacity of `outInfoUrl` in wide characters, including the null terminator, or `0` to skip it

**Returns:** `1` on success, `0` on failure, before `BugSplat_Init`, or if `title` is `NULL`. On failure, `*outCrashId` is set to `0` when `outCrashId` is non-`NULL`, and `outInfoUrl[0]` is set to the null terminator when `outInfoUrl` is non-`NULL` and `outInfoUrlChars` is greater than `0`.

**Note:** The C++ `BugSplat::PostFeedbackWithResult` returns a `FeedbackResult` that holds a `std::wstring`, which cannot cross the DLL boundary, so this entry point writes the URL into a buffer you supply. Added in version 8.0.0.

#### BugSplat\_GenerateDump

```c
void BugSplat_GenerateDump(void* exceptionPointers, int dumpType);
```

**Description:** Generates a crash report from caller-supplied exception information without terminating the process. Most applications do not need this. The exception filter installed by `BugSplat_Init` already captures unhandled crashes automatically. Use it only when you run your own exception handler and want to report a specific exception.

**Parameters:**

* `exceptionPointers` - An `EXCEPTION_POINTERS*` as provided by the OS inside an SEH `__except` filter (`GetExceptionInformation()`) or an unhandled-exception-filter callback. It is passed as `void*` so the header carries no `<windows.h>` dependency; it is not a value you construct.
* `dumpType` - A combination of Windows `MINIDUMP_TYPE` flags, or a negative value to use the SDK default.

#### BugSplat\_SetSuspendingState

```c
void BugSplat_SetSuspendingState(int suspending);
```

**Description:** Tells BugSplat that the application is suspending or has resumed. The C counterpart of `SetSuspendingState`: while it's suspending, the monitor skips hang detection and crash reports are not generated. Call it with `1` on `WM_POWERBROADCAST` / `PBT_APMSUSPEND` and with `0` on `PBT_APMRESUMEAUTOMATIC` or `PBT_APMRESUMESUSPEND`. It's cheap and safe to call from the UI thread. The monitor also ignores hangs that span system sleep on its own, so calling this is optional for sleep and hibernate.

**Parameters:**

* `suspending` - Non-zero while the application is suspending, `0` on resume

**Note:** Does nothing before `BugSplat_Init`. Added in version 8.3.0.

#### BugSplat\_AttachToProcess

```c
int BugSplat_AttachToProcess(unsigned long pid);
```

**Description:** The C counterpart of [`AttachToProcess`](#attachtoprocess), for a custom WER runtime exception module written in C. Call `BugSplat_Init` in the module, attach with the crashed application's process id, write the minidump into `BugSplat_GetCrashFolder()`, then call `BugSplat_PostCrash`.

**Parameters:**

* `pid` - Process id of the crashed application, from `GetProcessId` on the `hProcess` that WER passes to your module

**Returns:** `1` if attached. `0` before `BugSplat_Init`, if `pid` is `0` or the current process, or if that process has no running BugSplat monitor this SDK can talk to.

**Note:** Added in version 8.2.0.

#### BugSplat\_GetCrashFolder

```c
const wchar_t* BugSplat_GetCrashFolder(void);
```

**Description:** Returns the folder for the next crash report, the same path as `GetCrashFolder`. Copy the string: the folder changes after each upload.

**Returns:** The folder path, or `NULL` before `BugSplat_Init`.

**Note:** Added in version 8.2.0.

#### BugSplat\_PostCrash

```c
int BugSplat_PostCrash(void);
```

**Description:** Uploads the report in the crash folder and removes the folder on success. The C counterpart of `PostCrash`.

**Returns:** `1` once the monitor has handled the upload. `0` before `BugSplat_Init` or if no BugSplat monitor is available.

**Note:** Added in version 8.2.0.

#### C API Example

```c
#include "BugSplatC.h"

int main() {
    BugSplat_Init(L"MyDatabase", L"MyApp", L"1.0.0");
    BugSplat_SetUser(L"john.doe");
    BugSplat_SetEmail(L"john.doe@example.com");
    BugSplat_SetQuietMode(1);
    BugSplat_PostAllCrashesAsync();

    // Your application code here...

    return 0;
}
```

***

### Usage Examples

#### Basic Initialization

```cpp
#include "BugSplat.h"

// Initialize BugSplat
BugSplat BugSplat(L"MyDatabase", L"MyApp", L"1.0.0");
    
int main() {
   
    // Configure global CRT exception handling
    SetGlobalCRTExceptionBehavior();
    
    // Configure per-thread CRT exception handling
    SetPerThreadCRTExceptionBehavior();
    
    // Configure BugSplat options
    BugSplat.SetUser(L"john.doe");
    BugSplat.SetEmail(L"john.doe@example.com");
    BugSplat.SetKey(L"main-session");
    
    // Your application code here...
    
    return 0;
}
```

***

### Notes and Best Practices

1. **Initialization:** Always call `SetGlobalCRTExceptionBehavior()` once during application startup.
2. **Thread Safety:** Call `SetPerThreadCRTExceptionBehavior()` in each thread that should report crashes.
3. **File Attachments:** Be mindful of file sizes when adding attachments to avoid large uploads.
4. **Custom Attributes:** Use `SetAttribute()` to add context-specific information that will help with crash analysis.
