# Windows \(Native C++\) Dependencies

Our redistributable components are built from the Microsoft Visual C++ technology stack. In addition to the Microsoft libraries, the following third-party components are compiled into the BugSplat binaries you ship with your application.

| Name: | Version: | License: | URL: | Used in: |
| :--- | :--- | :--- | :--- | :--- |
| ATG Tool Kit \(ATGTK\) | 2022 snapshot | MIT | [https://github.com/microsoft/Xbox-GDK-Samples/tree/main/Kits/ATGTK](https://github.com/microsoft/Xbox-GDK-Samples/tree/main/Kits/ATGTK) | BugSplat.dll, BugSplat.lib, BugSplatMonitor.exe, BugSplatWer.dll |
| JSON for Modern C++ \(bundled with ATGTK\) | 3.6.1 | MIT | [https://github.com/nlohmann/json](https://github.com/nlohmann/json) | BugSplat.dll, BugSplat.lib, BugSplatMonitor.exe, BugSplatWer.dll |
| Miniz-Cpp | Commit 052335e | MIT | [https://github.com/tfussell/miniz-cpp](https://github.com/tfussell/miniz-cpp) | BugSplatMonitor.exe |
| miniz \(bundled with Miniz-Cpp\) | 1.15 | Public domain \(Unlicense\) | [https://github.com/richgel999/miniz](https://github.com/richgel999/miniz) | BugSplatMonitor.exe |
| MultipartEncoder | Commit ab4182a | MIT | [https://github.com/AndsonYe/MultipartEncoder](https://github.com/AndsonYe/MultipartEncoder) | BugSplatMonitor.exe |
| RapidJSON | 1.1.0 \(August 2022 master\) | MIT | [https://github.com/Tencent/rapidjson/](https://github.com/Tencent/rapidjson/) | BugSplatMonitor.exe, BugSplatWer.dll |
| Windows Template Library \(WTL\) | 10.0.8356 | MS-PL | [https://sourceforge.net/projects/wtl/](https://sourceforge.net/projects/wtl/) | BugSplatMonitor.exe |

Sample applications and build tools, such as `symbol-upload-windows.exe`, aren't redistributed with your application and aren't listed here.

