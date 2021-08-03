# Unreal Engine Windows 10 crash reporting bug fix

Microsoft’s Windows 10 Fall Creators update broke Unreal Engine’s ability to generate valid dmp files on some systems. Epic is working with Microsoft to fix this issue, but in the meantime there is a workaround.

If you include pre-FCU dlls next to your game’s executable they will load instead of the incompatible post-FCU dlls found in the GAC. The dll files you need \(dbghelp.dll, dbgcore.dll, dbgeng.dll\) can be found on earlier versions of Windows 10, or you can download copies we took from one of our machines [here](https://s3.amazonaws.com/bugsplat-public/dbgdlls.zip). More information about this issue can be found on the [UE4 Answer Hub](https://answers.unrealengine.com/questions/744933/unable-to-debug-dmp-files-created-in-windows-10.html) and on the [UE Forum](https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1396585-the-win-10-fall-creators-update-broke-debugging-dmp-files-generated-by-ue4-workaround-here).

We are working on a way to modify the UE4 source so that including these dlls is not required. We will update this post when that information becomes available.

