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

This should give you an .apk file in the `app/build/outputs/apk/embedNoRecord`
subdirectory that you can then sign and install on your phone.

Without Android Studio
----------------------

1. Install JDK 17 (not later not earlier).
2. Install CMake version 3.21 or later in your system.
3. Install Android SDK with SDK API 34 (34.x.y) and Android NDK 26.1.10909125
4. Open a terminal and navigate to the root folder of this project
5. Set the environment variable `ANDROID_HOME` to the path of your Android SDK

Then run
```
./gradlew assembleEmbedNoRecord
```

The first build may take upwards of 20 minutes, on consecutive builds, compiled libraries will have been cached for reuse.

This should give you an .apk file in the `app/build/outputs/apk/embedNoRecord`
subdirectory that you can then sign and install on your phone.

Signing
=======

Take all the info below with a grain of salt, it reflects the understanding of an individual that does not have a lot of cont

Why do we have to sign?
-----------------------

Android with its strict security requires all apps to be signed, otherwise they cannot be installed.

With either of the above methods to compile apks you will get a debug and release output.

The debug apk will be signed with a random(?) key and you can thus install and test it.

The release apk will be unsigned and you can therefore not install it, Android won't let you.

What does it do for us?
-----------------------

Signing serves the purpose of verifying the authenticity of authorship for delivering updates.

An app that was signed with a certain key can only be updated by an apk with the same application id and an equal/higher `version_code` (configured in gradle.properties) that was signed with the same key as the installed app.

Therefore, by signing with a secret key, none can maliciously try and publish an update looking like PA that installs over PA.

Why do we really want to sign instead of releasing debug-key signed versions?
-----------------------------------------------------------------------------

If we don't sign, it means that consecutively generated debug builds will have different signatures.

This means we cannot use them to update existing installations.

Presumably users would have to uninstall the previous version to install the new one and as far as I understand, that also wipes application data.  

Okay then, how do I sign?
-------------------------

### Using Android Studio

In Android Studio you may select the burger menu and choose `Build` -> `Generate Signed Bundle / APK`.

Pick `APK`, load in the java keystore file (jks), enter the password to access the key selection, pick the only available key, enter the password for the key and confirm.

Verify the path you want to export the apk too - I recommend creating a new directory at `app/build/outputs/apk/signed` to keep them easy to find.
Pick `embedNoRecordRelease` from the selection and click `Create` to finish.  
This should write the signed apk to `app/build/outputs/apk/signed/embedNoRecord/release/app-embed-noRecord-release.apk`


### Using uber-apk-signer

I haven't tried this myself so refer to the documentation when in doubt:  
https://github.com/patrickfav/uber-apk-signer

According to the demo you may supply a java keystore file (jks), its password, keyname and key password as commandline arguments to the tool to sign an apk using that specific key.

Use the apk from the `release` folder of the output of the earlier step.
Supposedly it won't work with the `debug` one because that one is already signed.