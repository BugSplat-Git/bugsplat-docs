# How do I get symbols for Proton / Wine frames?

Valve publishes a public symbol store for Proton (their Wine-based compatibility layer). Adding it lets BugSplat resolve Wine/Proton modules in call stacks for Windows games running under Proton (Steam Deck, Steam Play, etc.).

### Add the Proton symbol server

1. Navigate to the [Symbols](https://app.bugsplat.com/v2/database/symbols) page
2. Click **+ Add Server**
3. Host: `https://proton-archive.steamos.cloud/`

This works for Proton 9 and later. See also the [Unreal Engine](../../introduction/getting-started/integrations/game-development/unreal-engine.md#windows-games-running-under-proton-steam-deck-steam-play) and [Symbol Servers](../../introduction/development/working-with-symbol-files/symbol-servers.md) docs.

After adding the server, [reprocess](../how-tos/reprocessing-crashes.md) existing crashes that still show unresolved Proton/Wine frames.
