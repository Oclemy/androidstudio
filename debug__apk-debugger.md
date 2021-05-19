# Debug pre\-built APKs

- [Debug pre\-built APKs](#debug-pre-built-apks)
  - [Attach Kotlin/Java sources](#attach-kotlinjava-sources)
  - [Attach native debug symbols](#attach-native-debug-symbols)

Android Studio 3.0 and higher allow you to profile and debug APKs without having to build them from an Android Studio project. However, you need to make sure you’re using an APK with [debugging enabled](https://developer.android.com/studio/debug#enable-debug).

To start debugging an APK, click **Profile or debug APK** from the Android Studio Welcome screen. Or, if you already have a project open, click **File > Profile or Debug APK** from the menu bar. In the next dialog window, select the APK you want to import into Android Studio and click **OK**.

Android Studio then displays the unpacked APK files, similar to figure 1. This is not a fully decompiled set of files, though it does provide `.smali` files for a more readable version of the `.dex` files.

![](https://developer.android.com/studio/images/debug/import_apk-2X.png)

**Figure 1.** Importing a pre\-built APK into Android Studio.

**Note:** When you import an APK into Android Studio, the IDE creates a new project in your home directory under `ApkProjects/`, and makes a local copy of the target APK there.

The **Android** view in the Project pane allows you to inspect the following contents of your APK:

*   **APK file:** Double\-clicking the APK opens the APK analyzer.
*   **manifests:** Contains the app manifests that are extracted from the APK.
*   **java:** Contains Kotlin/Java code that Android Studio disassembles (into `.smali` files) from your APK’s DEX files. Each `.smali` file in this directory corresponds to a Kotlin/Java class.
*   **cpp:** If your app includes native code, this directory contains your APK’s native libraries (`.so` files).
*   **External Libraries:** Contains the Android SDK.

You can immediately [use the Android profiler](https://developer.android.com/studio/profile/android-profiler) to start testing your app's performance.

To debug your app’s Kotlin/Java code, you need to [attach the Kotlin/Java sources](#attach_java) and add breakpoints in the `.kt`/`.java` files. Similarly, to debug your native code, you must [attach native debug symbols](#attach_native).

## Attach Kotlin/Java sources

By default, Android Studio extracts Kotlin/Java code from your APK and saves it as `.smali` files. To debug the Kotlin/Java code using breakpoints, you need to point the IDE to the `.kt` or `.java` source files that correspond to the `.smali` files you want to debug.

To attach Kotlin/Java sources, proceed as follows:

1.  Double click on a `.smali` file from the **Project** pane (use the **Android** view). After opening the file, the editor displays a banner asking you to select the Kotlin/Java sources:

![Attach sources banner](https://developer.android.com/studio/images/debug/warning-attach-sources.png)

1.  Click **Attach Kotlin/Java Sources** from the banner at the top of the editor window.
2.  Navigate to the directory with the app’s Kotlin/Java source files and click **Open**.

In the **Project** window, the IDE replaces `.smali` files with their corresponding `.kt` or `.java` files. The IDE also includes inner classes automatically. You can now add breakpoints and [debug your app](https://developer.android.com/studio/debug) as you normally would.

## Attach native debug symbols

If your APK includes native libraries (`.so` files) that don't include debug symbols, the IDE shows you a banner, similar to the one shown in figure 1. You cannot debug the APK’s native code or use breakpoints without attaching debuggable native libraries.

If you build the native libraries in your APK with a [build ID](https://linux.die.net/man/1/ld), Android Studio checks whether the build ID in your symbol files matches the build ID in your native libraries and rejects the symbol files if there is a mismatch. If you did not build with a build ID, then providing incorrect symbol files may cause problems with debugging.

To attach debuggable native libraries, proceed as follows:

1.  If you haven't already done so, make sure to [download the NDK and tools](https://developer.android.com/ndk/guides#download-ndk).
2.  Under the **cpp** directory in the **Project** window (visible only if you've selected the **Android** view, as shown in figure 2), double\-click a native library file that doesn't include debug symbols. The editor shows a table of all the ABIs your APK supports.
3.  Click **Add** at the top right corner of the editor window.
4.  Navigate to the directory that includes the debuggable native libraries you want to attach and click **OK**.

If the APK and debuggable native libraries were built using a different workstation, you need to also specify paths to local debug symbols by following these steps:

1.  Add local paths to missing debug symbols by editing the field under the **Local Paths** column in the **Path Mappings** section of the editor window, as shown in figure 2. In most cases, you need only provide the path to a root folder, and Android Studio automatically inspects subdirectories to map additional sources. The IDE also automatically maps paths to a remote NDK to your local NDK download.
2.  Click **Apply Changes** in the **Path Mappings** section of the editor window.

![](https://developer.android.com/studio/images/debug/mapping_paths-2X.png)

**Figure 2.** Providing paths to local debug symbols.

You should now see the native source files in the **Project** window. Open those native files to add breakpoints and [debug your app](https://developer.android.com/studio/debug) as you normally would. You can also remove the mappings by clicking **Clear** in the **Path Mappings** section of the editor window.

**Known issue:** When attaching debug symbols to an APK, both the APK and debuggable `.so` files must be built using the same workstation or build server.

In Android Studio 3.6 and higher, you no longer need to create a new project when the APK is updated outside the IDE. Android Studio detects changes in the APK and gives you the option to re\-import it.

![](https://developer.android.com/studio/images/debug/import-updated-apk.png)

**Figure 3.** APKs updated outside Android Studio can be re\-imported.
