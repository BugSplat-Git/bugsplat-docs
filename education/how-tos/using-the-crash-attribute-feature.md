# Using the Crash Attribute Feature

BugSplat supports user-defined metadata, also known as Custom Crash Attributes, in crash and error report&#x73;**.** These attributes are composed of name-value pairs that can be seamlessly integrated into your application's crash reporting setup. Each custom attribute can be displayed as a column on the crashes page, with all attributes accessible on the crash detail pages.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Features:**

* **Display and Visibility:** Custom attributes can be viewed as columns on the crash report page, and are fully accessible on the crash detail pages.
* **Search, Group, Sort:** Custom attributes can be used in search, sorting, and grouping operations, just like other column values.

**Implementation:**

* Custom Crash Attributes are supported on all BugSplat crash platforms
* For **Windows** and **Xbox**, refer to the [BugSplat for Windows C++ documentation](../../introduction/getting-started/integrations/desktop/cplusplus/);
* For **PlayStation**, please refer to the documentation in the download found on the [Gaming Consoles](https://app.bugsplat.com/v2/database/integrations#consoles) tab on the Integrations page.
* For **Apple** platforms, refer to the [macOS](../../introduction/getting-started/integrations/desktop/macos.md)/[iOS](../../introduction/getting-started/integrations/mobile/ios.md#attributes) documentation.
* For **Linux**, **Crashpad**, **Qt**, **CMake**, and **Android,** BugSplat parses annotations as attributes. See the [Crashpad documentation](../../introduction/getting-started/integrations/cross-platform/crashpad/#initialization) for more info on how to add annotations.

For any issues or inquiries, please reach out to us at support@bugsplat.com. Our team is dedicated to helping you optimize your BugSplat integration and ensuring your application's crash reporting performs seamlessly.
