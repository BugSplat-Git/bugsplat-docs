# Common Symbols

If several versions of your app share the same symbol files, you may want to create a common symbol store to avoid uploading them each time. A common symbol store is useful if your application depends on third-party libraries, such as [Qt](https://www.qt.io/) or [Boost](https://www.boost.org/), where the `dll`, `exe`, or `pdb` files do not change when you rebuild your app.

Upload your symbols using a unique application name and version to create a common symbol store. The values you choose for application and version are arbitrary.  Since BugSplat stores symbols by GUID, these common symbols will be available to any version of your application. &#x20;





