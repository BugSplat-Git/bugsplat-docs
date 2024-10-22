---
description: >-
  BugSplat can reprocess crashes that were processed incorrectly due to missing
  symbols, mismatched symbols, or errors during initial processing.
---

# Reprocess Crashes

BugSplat processes crashes in two different modes, **Normal** and **Debug**. All crashes are processed in normal mode, and specific actions, such as requesting modules or debugger output will trigger the crash to process again in debug mode.

Sometimes, you may want to manually reprocess a crash if you recently uploaded new symbols or if your crash was not processed correctly. You can use the **Reprocess** button on the **Crash** page to kick off a reprocess operation manually

The **Reprocess** button will be disabled if the platform type does not support reprocessing.

BugSplat limits the concurrent reprocess operations for any given database to 50. If necessary, you can contact [support@bugsplat.com](mailto:support@bugsplat.com), and our support team can assist you with deleting bulk sets of crashes larger than 50.
