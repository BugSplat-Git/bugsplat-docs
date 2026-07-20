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

**Description:** Overrides the BugSplat crash type id stamped on uploaded crashes. By default native crashes are uploaded as `Native` (id `1`). Set this when a higher-level integration needs the server to process the crash differently — for example, a Unity IL2CPP integration sets `15` (`UnityNative`: "a native crash with an additional file containing the managed call stack"), which is the crash type the BugSplat backend uses to apply `LineNumberMappings.json` and symbolicate managed (C#) frames.

**Parameters:**

* `crashTypeId` - the BugSplat crash type id (default `1` = Native; `15` = UnityNative)

***

### Crash Detection & Reporting

#### GenerateDump

```cpp
void GenerateDump(LPEXCEPTION_POINTERS const exceptionPointers, 
                  MINIDUMP_TYPE dumpType = MINIDUMP_TYPE::MiniDumpNormal|MINIDUMP_TYPE::MiniDumpFilterTriage) const;
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

***

### Crash Management

#### PostCrash

```cpp
void PostCrash();
```

**Description:** Posts a single crash report and removes the folder after successful upload.

#### PostAllCrashes

```cpp
bool PostAllCrashes();
```

**Description:** Posts all pending crash reports. This method blocks until completion.

**Returns:** `true` if any crashes were posted, `false` otherwise

**Note:** Should only be called on a new thread to avoid blocking the main application.

#### PostAllCrashesAsync

```cpp
bool PostAllCrashesAsync();
```

**Description:** Posts all pending crash reports on a new background thread.

**Returns:** Always returns `true`

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

**Description:** Returns the folder path where current crash artifacts will be stored.

**Returns:** Path string in format `R:\BugSplat\{unique-guid-string}`

#### SetSuspendingState

```cpp
void SetSuspendingState(BOOL status);
```

**Description:** Sets the suspending state for crash handling.

**Parameters:**

* `status` - Suspension status flag

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
* **Static:** the C API is also compiled into both static flavors of `BugSplat.lib` (`lib\md` for `/MD` builds, `lib\mt` for `/MT` builds) — define `BUGSPLAT_STATIC` before including `BugSplatC.h`.

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

#### BugSplat\_GenerateDump

```c
void BugSplat_GenerateDump(void* exceptionPointers, int dumpType);
```

**Description:** Generates a crash report from caller-supplied exception information without terminating the process. Most applications do not need this — the exception filter installed by `BugSplat_Init` already captures unhandled crashes automatically. Use it only when you run your own exception handler and want to report a specific exception.

**Parameters:**

* `exceptionPointers` - An `EXCEPTION_POINTERS*` as provided by the OS inside an SEH `__except` filter (`GetExceptionInformation()`) or an unhandled-exception-filter callback. It is passed as `void*` so the header carries no `<windows.h>` dependency; it is not a value you construct.
* `dumpType` - A combination of Windows `MINIDUMP_TYPE` flags, or a negative value to use the SDK default.

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
