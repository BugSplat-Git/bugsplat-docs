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

**Note:** See myConsoleCrasher.cpp for XML schema examples.

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
void AddAttachment(const wchar_t* filepath);
```

**Description:** Adds a file to be included with crash reports.

**Parameters:**

* `filepath` - Full path to the file to attach

#### ClearAttachments

```cpp
void ClearAttachments();
```

**Description:** Removes all previously added file attachments.

***

### Crash Management

#### PostCrash

```cpp
void PostCrash(const wchar_t* crashFolder);
```

**Description:** Posts a single crash report and removes the folder after successful upload.

**Parameters:**

* `crashFolder` - Path to the folder containing crash artifacts

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

#### GetCrashStoragePath

```cpp
const wchar_t* GetCrashStoragePath();
```

**Description:** Returns the base path where crash reports are stored.

**Returns:** Path string to crash storage directory

#### GetLogFilePath

```cpp
const wchar_t* GetLogFilePath();
```

**Description:** Returns the path to the BugSplat log file.

**Returns:** Path string to log file

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
