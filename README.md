This is a fork of the love-android repository with the intent to facilitate the creation of Android builds for future maintainers of Panel Attack.

Please refer to the [official repository](https://github.com/love2d/love-android) for information about love-android instead.

Important:
----------

If you want to build from source, make sure to clone the submodules too. Often errors include missing
liblove.so and "Missing LÖVE" error when building. A proper way to clone this repository is:

```
git clone --recurse-submodules https://github.com/Endaris/love-android -b panel-attack
```

Add `--depth 1` if needed.

If you already cloned the repository but forgot to initialize the submodules, execute:

```
git submodule sync --recursive
git submodule update --init --force --recursive
```

In the repository directory. For the last command, add `--depth 1` if needed.

Compilation adventure
=====================

With Android Studio
-------------------

Install Android Studio **2022.3.1** or later. After opening it for the first time, open its SDK Manager
and on the tab "SDK Tools", tick "Show Package Details" then select NDK (Side By Side) version 26.1.10909125. After that, open
the repository root.

Select the gradle dialog from the side panel on the right, pick "Execute Gradle Task" and paste
```
gradlew assembleEmbedNoRecord
```

The first build may take upwards of 20 minutes, on consecutive builds, compiled libraries will have been cached for reuse.

Without Android Studio
----------------------

1. Install JDK 17 (not later not earlier).
2. Install CMake version 3.21 or later in your system.
3. Install Android SDK with SDK API 34 (34.x.y) and Android NDK 26.1.10909125
4. Open a terminal and navigate to the root folder of this project
5. Set the environment variable `ANDROID_HOME` to the path of your Android SDK

Then run
```
$ ./gradlew assembleEmbedNoRecord
```

The first build may take upwards of 20 minutes, on consecutive builds, compiled libraries will have been cached for reuse.

This should give you an .apk file in the `app/build/outputs/apk/embedNoRecord`
subdirectory that you can then sign and install on your phone.

Signing
=======

TBD