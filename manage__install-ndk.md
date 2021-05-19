# Install and configure the NDK and CMake

- [Install and configure the NDK and CMake](#install-and-configure-the-ndk-and-cmake)
  - [Install the NDK and CMake](#install-the-ndk-and-cmake)
    - [Configure a specific version of CMake](#configure-a-specific-version-of-cmake)
  - [Install a specific version of the NDK](#install-a-specific-version-of-the-ndk)
  - [Configure specific versions of the NDK in your project](#configure-specific-versions-of-the-ndk-in-your-project)
    - [Default NDK version per AGP version](#default-ndk-version-per-agp-version)

To compile and debug native code for your app, you need the following components:

*   The Android Native Development Kit (NDK): a set of tools that allows you to use C and C++ code with Android.
*   CMake: an external build tool that works alongside Gradle to build your native library. You do not need this component if you only plan to use ndk\-build.
*   LLDB: the debugger Android Studio uses to debug native code. By default, LLDB will be installed alongside Android Studio.

This page describes how to use Android Studio or the `sdkmanager` tool to download and install these components.

## Install the NDK and CMake

When you install the NDK, Android Studio selects the latest available NDK. For most projects, installing this default version of the NDK is sufficient. If your project needs one or more specific versions of the NDK, though, you can [download and configure specific versions](#specific-version). Doing so helps you ensure reproducible builds across projects that each depend on a specific version of the NDK. Android Studio installs all versions of the NDK in the `android-sdk/ndk/` directory.

To install CMake and the default NDK in Android Studio, do the following:

1.  With a project open, click **Tools > SDK Manager**.

2.  Click the **SDK Tools** tab.

3.  Select the **NDK (Side by side)** and **CMake** checkboxes.

    ![Image of SDK Manager](https://developer.android.com/studio/images/projects/install-NDK.png) **Figure 1:** The **SDK Tools** window showing the **NDK (Side by side)** option

    **Note:** If you have an NDK installed in the `ndk-bundle` folder, it appears in the list with the label **NDK**. If you are using Android Gradle plugin 3.5.0 or later, you can select this checkbox or clear it. Clearing the checkbox uninstalls the NDK, freeing up disk space, and causes the checkbox to disappear from the list. If you uninstall the legacy NDK, remove the `ndk.dir` value, which is now deprecated, from your projects' `local.properties` files.

4.  Click **OK**.

    A dialog box tells you how much space the NDK package consumes on disk.

5.  Click **OK**.

6.  When the installation is complete, click **Finish**.

7.  Your project automatically syncs the build file and performs a build. Resolve any errors that occur.

### Configure a specific version of CMake

The SDK Manager includes the 3.6.0 forked version of CMake and version 3.10.2. Projects that don't set a specific CMake version are built with CMake 3.10.2. To set the CMake version, add the following to your module's `build.gradle` file:

android { ...externalNativeBuild {cmake { ...version "cmake\-version" } }
}

If you want to use a CMake version 3.7 or higher that is not included by the SDK Manager, follow these steps:

1.  Download and install [CMake 3.7 or higher](https://cmake.org/download/) from the official CMake website.
2.  Specify the CMake version you want Gradle to use in your module's `build.gradle` file.
3.  Either add the path to the CMake installation to your `PATH` environment variable or include it in your project's `local.properties` file, as shown below. If Gradle is unable to find the version of CMake you specified in your `build.gradle` file, you get a build error.

    \# If you set this property, Gradle no longer uses PATH to find CMake.
    cmake.dir\="path\-to\-cmake"

4.  If you don't already have the Ninja build system installed on your workstation, go to the [official Ninja website](https://ninja-build.org/), and download and install the latest version of Ninja available for your OS. Make sure to also add the path to the Ninja installation to your `PATH` environment variable.

## Install a specific version of the NDK

To install a specific version of the NDK, do the following:

1.  With a project open, click **Tools > SDK Manager**.

2.  Click the **SDK Tools** tab.

3.  Select the **Show Package Details** checkbox.

4.  Select the **NDK (Side by side)** checkbox and the checkboxes below it that correspond to the NDK versions you want to install. Android Studio installs all versions of the NDK in the `android-sdk/ndk/` directory.

    **Note:** Preview releases (for example, canary and beta) of the NDK do not show up in this list unless you [change the update channel](https://developer.android.com/studio/preview/install-preview#change_your_update_channel) for Android Studio. You can [install an Android Studio preview](https://developer.android.com/studio/preview/install-preview) side\-by\-side with the stable version.

    ![Image of SDK Tools window](https://developer.android.com/studio/images/projects/install-NDK-sxs.png) **Figure 2:** The **SDK Tools** window showing the **NDK (Side by side)** options

    **Note:** If you have an NDK installed in the `ndk-bundle` folder, it appears in the list with the label **NDK**. If you are using Gradle version 3.5 or later, you can select this checkbox or clear it. Clearing it uninstalls the NDK installed, freeing up disk space, and cause the checkbox to disappear from the list. If you uninstall the legacy NDK, remove the `ndk.dir` value, which is now deprecated, from your projects' `local.properties` files.

5.  Click **OK**.

    A dialog box tells you how much space the NDK package(s) consumes.

6.  Click **OK**.

7.  When the installation is complete, click **Finish**.

8.  Your project automatically syncs the build file and performs a build. Resolve any errors that occur.

9.  [Configure each module](#apply-specific-version) with the version of the NDK you want it to use. When using Android Studio 3.6 or higher, if you do not specify the version, the Android Gradle plugin chooses a version that it is known to be compatible with.

## Configure specific versions of the NDK in your project

You may need to configure the version of the NDK in your project if one of the following is true:

*   Your project is inherited and you need to use specific versions of the NDK and the Android Gradle plugin (AGP). For more information, see [Configure the NDK for the Android Gradle plugin](https://developer.android.com/studio/projects/configure-agp-ndk).
*   You have multiple versions of the NDK installed and you want to use a specific one. In this case, specify the version using the `android.ndkVersion` property in the module's `build.gradle` file, as shown in the following code sample.

android {ndkVersion "major.minor.build"  // e.g.,  ndkVersion '21.3.6528147'
}

### Default NDK version per AGP version

Before release, each AGP version is thoroughly tested with the latest stable NDK release at that time. For AGP version 3.6 and above, that NDK version will be used to build your projects if you do **NOT** specify an NDK version in the `build.gradle` file. The default NDK version is documented inside the [AGP release notes](https://developer.android.com/studio/releases/gradle-plugin). The current default NDK versions are listed in the following table:

|  | **Android Studio/Gradle Plugin Version** |
| --- | --- |
|  | **4.1** | **4.0** | **3.6** | **3.5** | **3.4** |
| Default NDK version
specified for the version of AGP | 21.1.6352462 | 21.0.6113669 | 20.0.5594570 | No default specified |