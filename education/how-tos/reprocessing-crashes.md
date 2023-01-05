---
description: >-
  BugSplat can reprocess crashes in several different ways.  Users may want to
  reprocess a crash if it was processed incorrectly or if they've recently
  uploaded when they hadn't previously.
---

# Reprocess Crashes

Each time you navigate to a Crash page, BugSplat automatically starts a reprocess operation.&#x20;

Sometimes, you may want to reprocess a crash if you recently uploaded new symbols or if your crash was processed incorrectly.

You can use the reprocess button to kick off a reprocess operation manually&#x20;

The reprocess button will be disabled if your app doesn't have symbols, the symbols have been deleted, or the platform type does not support reprocessing&#x20;

BugSplat limits the concurrent reprocess operations for any given database to 10.  If necessary, you can contact [support@bugsplat.com](mailto:support@bugsplat.com), and our support team can assist you with deleting bulk sets of crashes larger than 10. &#x20;
