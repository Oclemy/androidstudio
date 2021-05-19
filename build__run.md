# Build and run your app

- [Build and run your app](#build-and-run-your-app)
  - [Change the run/debug configuration](#change-the-rundebug-configuration)
  - [Change the build variant](#change-the-build-variant)
  - [Build your project](#build-your-project)
  - [Monitor the build process](#monitor-the-build-process)
  - [Apply Changes](#apply-changes)
    - [Requirements](#requirements)
    - [Use Apply Changes](#use-apply-changes)
    - [Enable Run fallback for Apply Changes](#enable-run-fallback-for-apply-changes)
    - [Platform\-dependent changes](#platform-dependent-changes)
    - [Limitations of Apply Changes](#limitations-of-apply-changes)
      - [Code changes that require app restart](#code-changes-that-require-app-restart)
      - [Libraries and plugins](#libraries-and-plugins)
      - [Code that directly references content in an installed APK](#code-that-directly-references-content-in-an-installed-apk)

Android Studio sets up new projects to deploy to the Android Emulator or a connected device with just a few clicks. Once your app is installed, you can use [Apply Changes](#apply-changes) to deploy certain code and resource changes without building a new APK.

To build and run your app, follow these steps:

1.  In the toolbar, select your app from the run configurations drop\-down menu.
2.  From the target device drop\-down menu, select the device that you want to run your app on.

    ![Target device drop-down menu.](https://developer.android.com/studio/images/run/deploy-run-app.png)

    If you don't have any devices configured, then you need to either [connect a device via USB](https://developer.android.com/studio/run/device#connect) or [create an AVD](https://developer.android.com/studio/run/managing-avds#createavd) to use the [Android Emulator](https://developer.android.com/studio/run/emulator).

3.  Click **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png).

**Note:** You can also deploy your app in debug mode by clicking **Debug** ![](https://developer.android.com/studio/images/buttons/toolbar-debug.png). Running your app in debug mode allows you to set breakpoints in your code, examine variables and evaluate expressions at run time, and run debugging tools. To learn more, see [Debug your app](https://developer.android.com/studio/debug).

## Change the run/debug configuration

When you run your app for the first time, Android Studio uses a default run configuration. The run configuration specifies whether to deploy your app from an APK or an [Android App Bundle](https://developer.android.com/guide/app-bundle), the module to run, package to deploy, activity to start, target device, emulator settings, logcat options, and more.

The default run/debug configuration builds an APK, launches the default project activity, and uses the **Select Deployment Target** dialog for target device selection. If the default settings don't suit your project or module, you can customize the run/debug configuration, or even create a new one, at the project, default, and module levels. To edit a run/debug configuration, select **Run > Edit Configurations**. For more information, see [Create and Edit Run/Debug Configurations](https://developer.android.com/studio/run/rundebugconfig).

## Change the build variant

By default, Android Studio builds the debug version of your app, which is intended for use only during development, when you click **Run**.

To change the build variant Android Studio uses, select **Build** > **Select Build Variant** in the menu bar.

For projects without native/C++ code, the **Build Variants** panel has two columns: **Module** and **Active Build Variant**. The **Active Build Variant** value for the module determines which build variant the IDE deploys to your connected device and is visible in the editor.

![](https://developer.android.com/studio/images/run/build-variants.png)

**Figure 1.** The **Build Variants** panel has two columns for projects that do not have native/C++ code

To switch between variants, click the **Active Build Variant** cell for a module and choose the desired variant from the list field.

For projects with native/C++ code, the **Build Variants** panel has three columns: **Module**, **Active Build Variant**, and **Active ABI**. The **Active Build Variant** value for the module determines the build variant that the IDE deploys to your device and is visible in the editor. For native modules, the **Active ABI** value determines the [ABI](https://developer.android.com/ndk/guides/abis) that the editor uses, but does not impact what is deployed.

![](https://developer.android.com/studio/images/run/build-variants-ndk.png)

**Figure 2.** The **Build Variants** panel adds the **Active ABI** column for projects with native/C++ code

To change the build variant or ABI, click the cell for the **Active Build Variant** or **Active ABI** column and choose the desired variant or ABI from the list. After you change the selection, the IDE syncs your project automatically. Changing either column for an app or library module will apply the change to all dependent rows.

By default, new projects are set up with two build variants: a debug and release variant. You need to build the release variant to [prepare your app for public release](https://developer.android.com/studio/publish/preparing).

To build other variations of your app, each with different features or device requirements, you can [define additional build variants](https://developer.android.com/studio/build/build-variants).

## Build your project

The **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png) button builds and deploys your app to a device. However, to build your app to share or upload to Google Play, you'll need to use one of the options in the **Build** menu to compile parts or all of your project. Before you select any of the build options listed in table 1, make sure you first [select the build variant](#changing-variant) you want to use.

**Note:** Android Studio requires AAPT2 to build app bundles, which is enabled for new projects by default. However, to make sure it is enabled on existing projects, include `android.enableAapt2=true` in your `gradle.properties` file and restart the Gradle daemon by running `./gradlew --stop` from the command line.

**Table 1.** Build options in the **Build** menu.

| Menu Item | Description |
| --- | --- |
| **Make Module** | Compiles all source files in the selected module that have been modified since the last build, and all modules the selected module depends on recursively. The compilation includes dependent source files and any associated build tasks. You can select the module to build by selecting either the module name or one of its files in the **Project** window. This command does not generate an APK. |
| **Make Project** | Makes all modules. |
| **Clean Project** | Deletes all intermediate/cached build files. |
| **Rebuild Project** | Runs **Clean Project** for the selected build variant and produces an APK. |
| **Build Bundle(s) / APK(s) > Build APK(s)** |
Builds an APK of all modules in the current project for their selected variant. When the build completes, a confirmation notification appears, providing a link to the APK file and a link to analyze it in the [APK Analyzer](https://developer.android.com/studio/build/apk-analyzer).

If the build variant you've selected is a debug build type, then the APK is signed with a debug key and it's ready to install. If you've selected a release variant, then, by default, the APK is unsigned and you must manually [sign the APK](https://developer.android.com/studio/publish/app-signing). Alternatively, you can select **Build > Generate Signed Bundle / APK** from the menu bar.

Android Studio saves the APKs you build in `project-name/module-name/build/outputs/apk/`.

 |
| **Build Bundle(s) / APK(s) > Build Bundle(s)** |

Builds an [Android App Bundle](https://developer.android.com/guide/app-bundle) of all modules in the current project for their selected variant. When the build completes, a confirmation notification appears, providing a link to the app bundle and a link to analyze it in the [APK Analyzer](https://developer.android.com/studio/build/apk-analyzer).

If the build variant you've selected is a debug build type, then the app bundle is signed with a debug key, and you can [use `bundletool`](https://developer.android.com/studio/command-line/bundletool) to deploy your app from the app bundle to a connected device. If you've selected a release variant, then the app bundle is unsigned by default and you must manually sign it using [`jarsigner`](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html). Alternatively, you can select **Build > Generate Signed Bundle / APK** from the menu bar.

Android Studio saves the APKs you build in `project-name/module-name/build/outputs/bundle/`.

 |
| **Generate Signed Bundle / APK** | Brings up a dialog with a wizard to set up a new signing configuration, and build either a signed app bundle or APK. You need to sign your app with a release key before you can upload it to the Play Console. For more information about app signing, see [Sign your app](https://developer.android.com/studio/publish/app-signing). |

**Note:** The **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png) button builds an APK with `[testOnly="true"](https://developer.android.com/guide/topics/manifest/application-element#testOnly)`, which means the APK can only be installed via `[adb](https://developer.android.com/studio/command-line/adb)` (which Android Studio uses). If you want a debuggable APK that people can install without adb, select your debug variant and click **Build Bundle(s) / APK(s) > Build APK(s)**.

For details about the tasks that Gradle executes for each command, open the **Build** window as described in the next section. For more information about Gradle and the build process, see [Configure Your Build](https://developer.android.com/studio/build).

## Monitor the build process

You can view details about the build process by clicking **View > Tool Windows > Build** (or by clicking **Build** ![](https://developer.android.com/studio/images/buttons/window-build-2x.png) in the tool window bar). The window displays the tasks that Gradle executes in order to build your app, as shown in figure 3.

![](https://developer.android.com/studio/images/run/build_output_window-2x.png)

**Figure 3.** The Build output window in Android Studio

1.  **Build tab:** Displays the tasks Gradle executes as a tree, where each node represents either a build phase or a group of task dependencies. If you receive build\-time or compile\-time errors, inspect the tree and select an element to read the error output, as shown in figure 4. ![](https://developer.android.com/studio/images/run/build_output_window_error-2x.png)

    **Figure 4.** Inspect the Build output window for error messages

2.  **Sync tab:** Displays tasks that Gradle executes to sync with your project files. Similar to the **Build** tab, if you encounter a sync error, select elements in the tree to find more information about the error.
3.  **Restart:** Performs the same action as selecting **Build > Make Project** by generating intermediate build files for all modules in your project.
4.  **Toggle view:** Toggles between displaying task execution as a graphical tree and displaying more detailed text output from Gradle—this is the same output you see in the **Gradle Console** ![](https://developer.android.com/images/tools/as-gradlebutton.png) window on Android Studio 3.0 and earlier.

If your build variants use product flavors, Gradle also invokes tasks to build those product flavors. To view the list of all available build tasks, click **View > Tool Windows > Gradle** (or click **Gradle** ![](https://developer.android.com/studio/images/buttons/window-gradle.png) in the tool window bar).

If an error occurs during the build process, Gradle may recommend some command\-line options to help you resolve the issue, such as `--stacktrace` or `--debug`. To use command\-line options with your build process:

1.  Open the **Settings** or **Preferences** dialog:
    *   On Windows or Linux, select **File** > **Settings** from the menu bar.
    *   On Mac OSX, select **Android Studio** > **Preferences** from the menu bar.
2.  Navigate to **Build, Execution, Deployment** > **Compiler**.
3.  In the text field next to *Command\-line Options*, enter your command\-line options.
4.  Click **OK** to save and exit.

Gradle applies these command\-line options the next time you try building your app.

## Apply Changes

In Android Studio 3.5 and higher, Apply Changes lets you push code and resource changes to your running app without restarting your app—and, in some cases, without restarting the current activity. This flexibility helps you control how much of your app is restarted when you want to deploy and test small, incremental changes while preserving your device's current state. Apply Changes uses [capabilities in the Android JVMTI implementation](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) that are supported on devices running Android 8.0 (API level 26) or higher. To learn more about how Apply Changes works, see [Android Studio Project Marble: Apply Changes](https://medium.com/androiddevelopers/android-studio-project-marble-apply-changes-e3048662e8cd).

### Requirements

Apply Changes actions are only available when you meet the following conditions:

*   You build the APK of your app using a debug build variant.
*   You deploy your app to a target device or emulator that runs Android 8.0 (API level 26) or higher.

### Use Apply Changes

Use the following options when you want to deploy your changes to a compatible device:

Apply Changes and Restart Activity ![Apply Changes and Restart Activity icon](https://developer.android.com/studio/images/buttons/toolbar-apply-changes.svg)

Attempts to apply both your resource and code changes by restarting your activity but without restarting your app. Generally, you can use this option when you've modified code in the body of a method or modified an existing resource.

You can also perform this action by pressing `Ctrl+Alt+F10` (or `Control+Shift+Command+R` on macOS).

Apply Code Changes ![Apply Code Changes icon](https://developer.android.com/studio/images/buttons/toolbar-apply-code-changes.svg)

Attempts to apply only your code changes without restarting anything. Generally, you can use this option when you've modified code in the body of a method but you have not modified any resources. If you've modified both code and resources, use **Apply Changes and Restart Activity** instead.

You can also perform this action by pressing `Ctrl+F10` (or `Control+Command+R` on macOS).

Run ![Run icon](https://developer.android.com/studio/images/buttons/toolbar-run.png)

Deploys all changes and restarts the app. Use this option when the changes that you have made cannot be applied using either of the Apply Changes options. To learn more about the types of changes that require an app restart, see [Limitations of Apply Changes](#apply-changes-limitations).

### Enable Run fallback for Apply Changes

After you've clicked either **Apply Changes and Restart Activity** or **Apply Code Changes**, Android Studio builds a new APK and determines whether the changes can be applied. If the changes can't be applied and would cause Apply Changes to fail, Android Studio prompts you to Run ![Run icon](https://developer.android.com/studio/images/buttons/toolbar-run.png) your app again instead. However, if you don't want to be prompted every time this occurs, you can configure Android Studio to automatically rerun your app when changes can't be applied.

To enable this behavior, follow these steps:

1.  Open the **Settings** or **Preferences** dialog:

    *   On Windows or Linux, select **File > Settings** from the menu bar.
    *   On macOS, select **Android Studio > Preferences** from the menu bar.
2.  Navigate to **Build, Execution, Deployment > Deployment**.

3.  Select the checkboxes to enable automatic Run fallback for either of the Apply Changes actions.

4.  Click **OK**.

**Note:** Some types of changes don't cause Apply Changes to fail, but still require you to restart your app manually before you can see those changes. For example, if you make changes to an activity's [`onCreate()`](https://developer.android.com/reference/android/app/Activity#onCreate(android.os.Bundle)) method, those changes only take effect after the activity is relaunched, so you must restart your app to see those changes.

### Platform\-dependent changes

Some features of Apply Changes depend on specific versions of the Android platform. To apply these kinds of changes, your app must be deployed to a device running that version of Android (or higher).

| Type of change | Minimum platform version |
| --- | --- |
| Adding a method | Android 11 |

### Limitations of Apply Changes

Apply Changes is designed to speed up the app deployment process. However, there are some limitations for when it can be used. If you encounter any issues while using Apply Changes, [file a bug](https://issuetracker.google.com/issues/new?component=192708).

#### Code changes that require app restart

Some code and resource changes cannot be applied until the app is restarted, including the following:

*   Adding or removing a field
*   Removing a method
*   Changing method signatures
*   Changing modifiers of methods or classes
*   Changing class inheritance
*   Changing values in enums
*   Adding or removing a resource
*   Changing the app manifest
*   Changing native libraries (SO files)

#### Libraries and plugins

Some libraries and plugins automatically make changes to your app's manifest files or to resources that are referenced in the manifest. These automatic updates can interfere with Apply Changes in the following ways:

*   If a library or plugin makes changes to your app's manifest, you can't use either **Apply Code Changes** ![Apply Code Changes icon](https://developer.android.com/studio/images/buttons/toolbar-apply-code-changes.svg) or **Apply Changes and Restart Activity** ![Apply Changes and Restart Activity icon](https://developer.android.com/studio/images/buttons/toolbar-apply-changes.svg) and have to restart your app before you can see your changes.
*   If a library or plugin makes changes to your app's resource files, you can't use **Apply Code Changes** ![Apply Code Changes icon](https://developer.android.com/studio/images/buttons/toolbar-apply-code-changes.svg), and you must use **Apply Changes and Restart Activity** ![Apply Changes and Restart Activity icon](https://developer.android.com/studio/images/buttons/toolbar-apply-changes.svg) to see your changes.

You can avoid these limitations by disabling all automatic updates for your debug build variants.

For example, Crashlytics updates app resources with a unique build ID during every build, which prevents you from using **Apply Code Changes** ![Apply Code Changes icon](https://developer.android.com/studio/images/buttons/toolbar-apply-code-changes.svg) and requires you to restart your app's activity to see your changes. You can [disable this behavior](https://developer.android.com/studio/build/optimize-your-build#disable-crashlytics-build-id) so that you can use **Apply Code Changes** alongside Crashlytics with your debug builds.

#### Code that directly references content in an installed APK

If your code directly references content from your app's APK that's installed on the device, that code can cause crashes or misbehave after clicking **Apply Code Changes** ![Apply Code Changes icon](https://developer.android.com/studio/images/buttons/toolbar-apply-code-changes.svg). This behavior occurs because when you click **Apply Code Changes**, the underlying APK on the device is replaced during installation. In these cases, you can click **Apply Changes and Restart Activity** ![Apply Changes and Restart Activity icon](https://developer.android.com/studio/images/buttons/toolbar-apply-changes.svg) or **Run** ![Run icon](https://developer.android.com/studio/images/buttons/toolbar-run.png), instead.