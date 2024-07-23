# Managing Symbol Space

This document outlines how symbols are stored, the automatic cleanup processes, and how to manage your symbol space.

Symbols play a crucial role in debugging crash reports by providing meaningful stack traces. Managing the symbol space efficiently in your BugSplat account ensures optimal performance and resource utilization. This document outlines how symbols are stored, the automatic cleanup processes in place, and how you can manually manage your symbol store.

### Symbol Store Creation

When you upload symbols to BugSplat, a "symbol store" is created. A symbol store is a collection of symbols identified by their application name and version. BugSplat groups these symbols together for easy reference and management.

#### Automatic Cleanup of Symbol Stores

BugSplat implements automatic cleanup rules to manage symbol stores and remove unneeded symbols. Here are the key rules:

1. **Symbol Size Cleanup Limit**:
   * If your database contains less than 5 gigabytes of symbol data, BugSplat won't perform any automatic cleanup.
2. **New Symbol Data Cleanup**:
   * Newly uploaded symbols not referenced by any crash report within 15 days of being posted will be removed.&#x20;
3. **Inactivity-Based Removal**:
   * BugSplat will automatically remove symbol stores that haven't been accessed recently.  Symbol stores not referenced by crash reports in over 90 days will be automatically removed.

#### Manual Removal of Symbol Stores

Using BugSplat's web application, you can manually remove symbol stores.&#x20;

1. Navigate to the [Versions](https://app.bugsplat.com/v2/versions) page.  Select the symbol stores that you wish to delete.&#x20;
2.  Press the 'Delete Symbols' button



    <figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

#### Manage Symbols Using our API

If BugSplat's symbol cleanup rules aren't optimal for your team, you can implement your own cleanup logic using the BugSplat API.

#### Best Practices for Managing Symbol Space

To ensure efficient management of your symbol space, consider the following best practices:

* **Regularly Monitor Symbol Usage**:
  * Monitor the size of your symbol database and the frequency of symbol access to anticipate when automatic cleanup might occur.
* **Upload Only Necessary Symbols**:
  * Avoid uploading redundant or unnecessary symbols. This helps keep your symbol database lean and manageable.
* **Change Versions Frequently**
  * The best practice is to assign a unique version to each build.  It allows BugSplat's automated cleanup rules to remove symbols specific to each build.
* **Use the Manual Cleanup Option When Needed**:
  * Periodically review your symbol stores and manually delete those no longer needed.&#x20;

####
