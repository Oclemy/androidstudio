# Update the IDE and SDK Tools

- [Update the IDE and SDK Tools](#update-the-ide-and-sdk-tools)
  - [Update your IDE and change channels](#update-your-ide-and-change-channels)
    - [Delete unused Android Studio directories](#delete-unused-android-studio-directories)
  - [Update your tools with the SDK Manager](#update-your-tools-with-the-sdk-manager)
    - [Recommended packages](#recommended-packages)
    - [Edit or add SDK tool sites](#edit-or-add-sdk-tool-sites)
    - [Auto\-download missing packages with Gradle](#auto-download-missing-packages-with-gradle)

Once you install Android Studio, it's easy to keep the Android Studio IDE and Android SDK tools up to date with automatic updates and the Android SDK Manager.

## Update your IDE and change channels

Android Studio notifies you with a small bubble dialog when an update is available for the IDE, but you can manually check for updates by clicking **Help** > **Check for Update** (on Mac, **Android Studio** > **Check for Updates**).

Updates for Android Studio are available from the following release channels:

*   **Canary channel**: These are bleeding\-edge releases, updated roughly weekly, and available for download at [developer.android.com/studio/preview](https://developer.android.com/studio/preview).

    In addition to receiving canary versions of Android Studio, you will also receive preview versions of other SDK tools, including the Android Emulator.

    Although these builds are subject to more bugs, they do get tested and we want to offer them so you can try new features and provide feedback. This channel is **not recommended for production development**.

*   **Dev channel**: These are hand\-picked canary builds that survived a full round of internal testing.
*   **Beta channel**: These are release candidates based on stable canary builds, released to get feedback before going into the stable channel.
*   **Stable channel**: The official stable release that is available for download at [developer.android.com/studio](https://developer.android.com/studio).

If you'd like to try one of the preview channels (Canary, Dev, or Beta) while still using the Stable build for your production Android projects, you can [install both side by side](https://developer.android.com/studio/preview/install-preview).

To change the update channel for an existing install, proceed as follows:

1.  Open the **Preferences** window by clicking **File > Settings** (on Mac, **Android Studio > Preferences**).
2.  In the left panel, click **Appearance & Behavior > System Settings > Updates**.
3.  Be sure that **Automatically check for updates** is checked, then select a channel from the drop\-down list (see figure 1).
4.  Click **Apply** or **OK**.

![](https://developer.android.com/studio/images/preferences-updates_2x.png)

**Figure 1.** The Android Studio Updates preferences.

### Delete unused Android Studio directories

![](https://developer.android.com/studio/images/intro/delete-unused-directories-dialog-2x.png)

When you run a major version of Android Studio for the first time, it looks for directories containing caches, settings, indices, and logs for versions of Android Studio for which a corresponding installation can’t be found. The **Delete Unused Android Studio Directories** dialog then displays locations, sizes, and last\-modified times of these unused directories and provides an option to delete them.

The directories Android Studio considers for deletion are listed below:

*   Linux: `~/.AndroidStudio[Preview]_version_`
*   Mac: `~/Library/{Preferences, Caches, Logs, Application Support}/AndroidStudio[Preview]_version_`
*   Windows: `%USER%\.AndroidStudio[Preview]_version_`

## Update your tools with the SDK Manager

The Android SDK Manager helps you download the SDK tools, platforms, and other components you need to develop your apps. Once downloaded, you can find each package in the directory indicated as the **Android SDK Location**, shown in figure 2.

To open the SDK Manager from Android Studio, click **Tools > SDK Manager** or click **SDK Manager** ![](https://developer.android.com/studio/images/buttons/toolbar-sdk-manager.png) in the toolbar. If you're not using Android Studio, you can download tools using the [`sdkmanager` ](https://developer.android.com/studio/command-line/sdkmanager) command\-line tool.

When an update is available for a package you already have, a dash ![](https://developer.android.com/studio/images/sdk-manager-icon-update_2-0_2x.png) appears in the check box next to the package.

*   To update an item or install a new one, click the check box so it shows a checkmark.
*   To uninstall a package, click to clear the check box.

Pending updates are indicated in the left column with a download icon ![](https://developer.android.com/images/tools/studio-sdk-dwnld-icon.png). Pending removals are indicated with a red cross ![](https://developer.android.com/images/tools/studio-sdk-removal-icon.png).

To update the selected packages, click **Apply** or **OK**, then agree to any license agreements.

![](https://developer.android.com/studio/images/sdk-manager-tools_2x.png)

**Figure 2.** The Android SDK Manager.

### Recommended packages

You should give special consideration to the following tools in the **SDK Tools** tab:

**Android SDK Build\-Tools**

**Required.** Includes tools to build Android apps. See the [SDK Build Tools release notes](https://developer.android.com/studio/releases/build-tools).

**Android SDK Platform\-Tools**

**Required.** Includes various tools required by the Android platform, including the [adb](https://developer.android.com/studio/command-line/adb) tool.

**Android SDK Tools**

**Required.** Includes essential tools such as ProGuard. See the [SDK Tools Release Notes](https://developer.android.com/studio/releases/sdk-tools).

**Android Emulator**

Recommended. A QEMU\-based device\-emulation tool that you can use to debug and test your applications in an actual Android runtime environment. See the [Android Emulator release notes](https://developer.android.com/studio/releases/emulator).

**Note:** Most API libraries that were previously provided by the **Support Repository** packages (such as the Android Support Library, Constraint Layout, Google Play services, and Firebase) are now instead available from Google's Maven repository. Projects created with Android Studio 3.0 and higher automatically include this repository in the build configuration. If you're using an older project, you must manually [add Google's Maven repository](https://developer.android.com/studio/build/dependencies#google-maven) to your `build.gradle` file.

In the **SDK Platforms** tab, you must also install at least one version of the Android platform. Each version provides several different packages. To download only those that are required, click the check box next to the version name.

To see all available packages for each Android platform, click **Show Package Details** at the bottom of the window. Within each platform version, you'll find the following packages:

**Android SDK Platform**

**Required.** *At least one platform* is required in your environment so you're able to compile your application. In order to provide the best user experience on the latest devices, use the latest platform version as your build target. You'll still be able to run your app on older versions, but you must build against the latest version in order to use new features when running on devices with the latest version of Android.

**Intel** or **ARM System Images**

Recommended. The system image is required in order to run the [Android Emulator](https://developer.android.com/tools/devices/emulator). Each platform version contains the supported system images. You can also download system images later when creating Android Virtual Devices (AVDs) in the [AVD Manager](https://developer.android.com/studio/run/managing-avds). Select either Intel or ARM based on your development computer's processor.

**Note:** If you plan to use APIs from [Google Play services](https://developers.google.com/android/) (including Firebase), you must use either the **Google APIs** system image or the **Google Play** system image (the latter includes the Play Store app).

The above list is not comprehensive and you can [add other sites](#AddingSites) to download additional packages from third parties.

In some cases, an SDK package may require a specific minimum revision of another tool. If so, the SDK Manager notifies you with a warning and adds the dependencies to your list of downloads.

**Tip:** You can also customize the `build.gradle` file so each project uses a specific build chain and compilation options. For more information see, [Configuring Gradle Builds](https://developer.android.com/tools/building/configuring-gradle).

### Edit or add SDK tool sites

To manage which SDK sites Android Studio checks for Android tools and third party tool updates, click the **SDK Update Sites** tab. You can add other sites that host their own tools, then download the packages from those sites.

For example, a mobile carrier or device manufacturer might offer additional API libraries that are supported by their own Android\-powered devices. To develop using their libraries, you can install their Android SDK package by adding their SDK tools URL to the SDK Manager in the **SDK Update Sites**.

If a carrier or device manufacturer has hosted an SDK add\-on repository file on their website, follow these steps to add their site to the Android SDK Manager:

1.  Click the **SDK Update Sites** tab.
2.  Click **Add** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) at the bottom of the window.
3.  Enter the name and URL of the third party site, then click **OK**.
4.  Make sure the checkbox is selected in the **Enabled** column.
5.  Click **Apply** or **OK** .

Any SDK packages available from the site now appear in the **SDK Platforms** or **SDK Tools** tabs, as appropriate.

### Auto\-download missing packages with Gradle

When you run a build [from the command line](https://developer.android.com/studio/build/building-cmdline), or when using Android Studio 3.3 or later, Gradle can automatically download missing SDK packages that a project depends on, as long as the corresponding SDK license agreements have already been accepted using the SDK Manager.

When you accept the license agreements using the SDK Manager, Android Studio creates a licenses directory inside the SDK home directory. This licenses directory is necessary for Gradle to auto\-download missing packages.

**Note:** Accepting the license agreements using the `android` command line tool does not create this licenses directory. You must first accept the agreements using the SDK Manager to be able to use this feature.

If you have accepted the license agreements on one workstation, but wish to build your projects on a different one, you can export your licenses by copying over the accepted licenses directory. To copy the licenses to another machine, follow these steps:

1.  On a machine with Android Studio installed, click **Tools > Android > SDK Manager**. At the top of the window, note the Android SDK Location.
2.  Navigate to that directory and locate the `licenses/` directory inside it. (If you do not see a `licenses/` directory, return to Android Studio and update your SDK tools, making sure to accept the license agreements. When you return to the Android SDK home directory, you should now see the directory.)
3.  Copy the entire `licenses/` directory and paste it into the Android SDK home directory on the machine where you wish to build your projects.

Gradle will now be able to automatically download missing packages your project depends on.

Note that this feature is automatically disabled for builds you run from Android Studio, as the SDK manager handles downloading missing packages for the IDE. You can also manually disable this feature by setting `android.builder.sdkDownload=false` in the `gradle.properties` file for your project.