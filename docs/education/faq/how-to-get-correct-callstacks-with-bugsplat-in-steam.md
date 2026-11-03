# How to get correct callstacks with BugSplat in Steam

Steam DRM (Digital Rights Management) can interfere with the creation of symbolicated call stacks.  If you are using Steam DRM, you should upload only the versions of your .exe/.dll files that have been processed by DRM.  Uploading the non-DRM version will break symbolication. &#x20;

Alternatively, you can invoke the Steam DRM wrapper in 'Compatibility Mode', which disables obfuscation. Learn more about the DRM documentation [here](https://partner.steamgames.com/doc/features/drm).

