# How Do I Remove Symbol Files?

{% hint style="warning" %}
BugSplat will automatically remove symbols according to the rules below. BugSplat customers are responsible for long-term symbol storage.
{% endhint %}

There are a few ways that symbol files can be removed from BugSplat.

### Automatically

BugSplat will automatically remove unreferenced symbols in large symbol sets. If your database contains more than 5 gigabytes of symbol data, our cleanup algorithm will automatically remove symbols not referenced by a crash report in more than 90 days. Additionally, newly posted symbols not referenced by a crash report within 15 days will be removed.

### Manually

Symbol stores can be removed by navigating to the [Versions](https://app.bugsplat.com/v2/versions) page. Click the checkbox next to the versions containing symbols you want to remove. Click the **Delete Symbols** button to remove the symbols for the selected versions.

<figure><img src="../../.gitbook/assets/output (1) (1).gif" alt=""><figcaption><p>Removing Symbols Manually</p></figcaption></figure>
