# Common Symbols

If several versions of your app share the same symbol files, you might want to create a common symbol store so you don't have to upload them each time. A common symbol store is useful if your application depends on third-party libraries, such as [Qt](https://www.qt.io/) or [Boost](https://www.boost.org/), where the `dll`, `exe`, or `pdb` files do not change when you rebuild your app.

Upload your symbols using a new application name and version to create a common symbol store. The values you choose for application and version are arbitrary. Once you've uploaded your common symbols, navigate to the [Settings](https://app.bugsplat.com/v2/database/symbols?database=Fred) page. Under **Database > Symbols > Common Symbol Library** enter the application and version values you used when you uploaded your common symbols. Click **Update** to persist your changes.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Please note that these symbols will be used in every crash report, so be mindful that uploading a lot of common symbols could slow down crash processing times.
