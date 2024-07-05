# Linux

## Overview

BugSplat recommends using [Crashpad](https://chromium.googlesource.com/crashpad/crashpad) for Linux crash reporting. Crashpad is Google's latest open-source crash reporting tool. It is the successor to the popular Breakpad crash reporter and allows you to submit minidumps to a configured URL after a crash occurs in your product.

Before continuing with the tutorial, please review our [myUbuntuCrasher](https://github.com/BugSplat-Git/myUbuntuCrasher) sample application.

## Tutorial

The first step in integrating with Crashpad is ensuring your system has all the required dependencies. These dependencies include `git`, `python`, `llvm` and `clang++`. The following snippet will download and install all the dependencies on an Ubuntu system:

```
sudo apt-get install git
sudo apt-get install python
sudo apt-get install llvm
sudo apt-get install clang
```

Next, you will need to build and integrate Crashpad with your application. For a step-by-step guide on how to build and integrate Crashpad please see this [doc](../cross-platform/crashpad/).

Once you've built and integrated Crashpad, you must ensure that your application is built with symbolic information and a build identifier. Symbolic information is required to map the stack trace in the minidump to function names and line numbers in your application's source. A build identifier is required so that `minidump_stackwalk` can match modules loaded at runtime with the corresponding `.sym` file.

{% hint style="info" %}
When specifying the Crashpad libraries `libbase.a` must be the last library argument specified otherwise your code will not compile.
{% endhint %}

If you are building with `clang++`, specify the `-g` flag to ensure the output executable contains symbolic information for debugging. Additionally, when building with `clang++` you must pass the `-Wl,--build-id` argument to ensure the linker creates a build identifier in the output executable. The following [script](https://github.com/BugSplat-Git/myUbuntuCrasher/blob/master/scripts/compile.sh) from [myUbuntuCrasher](https://github.com/BugSplat-Git/myUbuntuCrasher) demonstrates how to link the Crashpad libraries and output an executable with symbolic information using `clang++`:

```
#!/bin/bash
source exports.sh

clang++ -pthread $PROJECT_DIR/main.cpp \
  $CRASHPAD_DIR/lib/libclient.a \
  $CRASHPAD_DIR/lib/libcommon.a \
  $CRASHPAD_DIR/lib/libutil.a \
  $CRASHPAD_DIR/lib/libbase.a \
  -I$CRASHPAD_DIR/include \
  -I$CRASHPAD_DIR/include/third_party/mini_chromium/mini_chromium \
  -o$OUT_DIR/$MODULE_NAME \
  -g \
  -Wl,--build-id
```

Finally, you will need to generate and upload `.sym` files to BugSplat. BugSplat's symbol-upload tool can be used conveniently to generate and upload `.sym` files with a single terminal command. Download a copy of symbol-upload from [GitHub](https://github.com/BugSplat-Git/symbol-upload) or install it via [npm](https://npmjs.com/package/@bugsplat/symbol-upload).

Once you have symbol-upload downloaded or installed, modify the following command to suit your needs.

```bash
$CRASHPAD_DIR/tools/symbol-upload-linux -b $BUGSPLAT_DATABASE \
    -a $BUGSPLAT_APP_NAME \
    -v $BUGSPLAT_APP_VERSION \
    -u $BUGSPLAT_EMAIL \
    -p $BUGSPLAT_PASSWORD \
    -d $PROJECT_DIR/out \
    -f $MODULE_NAME \
    --dumpSyms
```

It's crucial that you re-upload symbols each time you build your application. Otherwise, BugSplat cannot generate function names and line numbers for the crash report. Symbols should be uploaded using a new version for every build to ensure your crashes are processed quickly.

If you've set everything up correctly, your crash report should look like this:

<figure><img src="../../../../.gitbook/assets/image (51).png" alt=""><figcaption><p>MyUbuntuCrasher Crash on BugSplat</p></figcaption></figure>
