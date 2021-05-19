# Add C and C++ code to your project

- [Add C and C++ code to your project](#add-c-and-c-code-to-your-project)
  - [Download the NDK and build tools](#download-the-ndk-and-build-tools)
  - [Create a new project with C/C++ support](#create-a-new-project-with-cc-support)
    - [Build and run the sample app](#build-and-run-the-sample-app)
  - [Create new C/C++ source files](#create-new-cc-source-files)
  - [Additional resources](#additional-resources)
    - [Codelabs](#codelabs)

You can add C and C++ code to your Android project by placing the code into a **cpp** directory in your project module. When you build your project, this code is compiled into a native library that Gradle can package with your APK. Your Java or Kotlin code can then call functions in your native library through the Java Native Interface (JNI). To learn more about using the JNI framework, read [JNI tips for Android](https://developer.android.com/training/articles/perf-jni).

Android Studio supports CMake, which is good for cross\-platform projects, and [ndk\-build](https://developer.android.com/ndk/guides/ndk-build), which can be faster than CMake but only supports Android. Using both CMake and ndk\-build in the same module is not currently supported.

If you want to import an existing ndk\-build library into your Android Studio project, learn how to [link Gradle to your native library project](https://developer.android.com/studio/projects/gradle-external-native-builds).

This page shows you how to [set up Android Studio](#download-ndk) with the necessary build tools, [create a new project](#new-project) with C/C++ support, and [add new C/C++ files](#create-sources) to your project.

If instead you want to add native code to an existing project, you need to follow these steps:

1.  [Create new native source files](#create-sources) and add them to your Android Studio project.
    *   You can skip this step if you already have native code or want to import a prebuilt native library.
2.  [Configure CMake](https://developer.android.com/studio/projects/configure-cmake) to build your native source code into a library. You also require this build script if you are importing and linking against prebuilt or platform libraries.
    *   If you have an existing native library that already has a `CMakeLists.txt` build script, or uses ndk\-build and includes an [`Android.mk`](https://developer.android.com/ndk/guides/android_mk) build script, you can skip this step.
3.  [Configure Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds) by providing a path to your CMake or ndk\-build script file. Gradle uses the build script to import source code into your Android Studio project and package your native library (the SO file) into the APK.

Once you configure your project, you can access your native functions from Java or Kotlin code using the [JNI framework](http://docs.oracle.com/javase/7/docs/technotes/guides/jni/spec/jniTOC.html). To build and run your app, simply click **Run** ![run then run app from the menu bar](https://developer.android.com/studio/images/buttons/toolbar-run.png).

**Note:** If your existing project uses the deprecated `ndkCompile` tool, you should migrate to using either CMake or ndk\-build. To learn more, go to the section about how to [Migrate from ndkCompile](#ndkCompile).

**Attention experimental Gradle users:** Consider [migrating to plugin version 2.2.0 or higher](http://tools.android.com/tech-docs/new-build-system/gradle-experimental/migrate-to-stable), and using CMake or ndk\-build to build your native libraries if any of the following apply to you: Your native project already uses CMake or ndk\-build; you would rather use a stable version of the Gradle build system; or you want support for add\-on tools, such as [CCache](https://ccache.samba.org/). Otherwise, you can continue to [use the experimental version of Gradle and the Android plugin](http://tools.android.com/tech-docs/new-build-system/gradle-experimental).

## Download the NDK and build tools

To compile and debug native code for your app, you need the following components:

*   *[The Android Native Development Kit (NDK)](https://developer.android.com/ndk)*: a toolset that allows you to use C and C++ code with Android, and provides platform libraries that allow you to manage native activities and access physical device components, such as sensors and touch input.
*   *[CMake](https://cmake.org/)*: an external build tool that works alongside Gradle to build your native library. You do not need this component if you only plan to use ndk\-build.
*   *[LLDB](http://lldb.llvm.org/)*: the debugger Android Studio uses to [debug native code](https://developer.android.com/studio/debug).

For information on installing these components, see [Install and configure the NDK, CMake, and LLDB](https://developer.android.com/studio/projects/install-ndk).

## Create a new project with C/C++ support

Creating a new project with support for native code is similar to [creating any other Android Studio project](https://developer.android.com/studio/projects/create-project), but there is an additional step:

1.  In the **Choose your project** section of the wizard, select the **Native C++** project type.
2.  Click **Next**.
3.  Complete all other fields in the next section of the wizard.
4.  Click **Next**.
5.  In the **Customize C++ Support** section of the wizard, you can customize your project with the **C++ Standard** field. Use the drop\-down list to select which standardization of C++ you want to use. Selecting **Toolchain Default** uses the default CMake setting.
6.  Click **Finish**.

After Android Studio finishes creating your new project, open the **Project** pane from the left side of the IDE and select the **Android** view. As shown in figure 2, Android Studio adds the **cpp** group:

![](https://developer.android.com/studio/images/projects/cpp-project-view_2-2_2x.png)

**Figure 2.** Android view groups for your native sources and external build scripts.

**Note:** This view does not reflect the actual file hierarchy on disk, but groups similar files to simplify navigating your project.

The **cpp** group is where you can find all the native source files, headers, build scripts for CMake or ndk\-build, and prebuilt libraries that are a part of your project. For new projects, Android Studio creates a sample C++ source file, `native-lib.cpp`, and places it in the `src/main/cpp/` directory of your app module. This sample code provides a simple C++ function, `stringFromJNI()`, that returns the string "Hello from C++". You can learn how to add additional source files to your project in the section about how to [Create new native source files](#create-sources).

Similar to how `build.gradle` files tell Gradle how to build your app, CMake and ndk\-build require a build script to know how to build your native library. For new projects, Android Studio creates a CMake build script, `CMakeLists.txt`, and places it in your module’s root directory. To learn more about the contents of this build script, read [Configure CMake](https://developer.android.com/studio/projects/configure-cmake).

### Build and run the sample app

When you click **Run** ![run then run app from the menu bar](https://developer.android.com/studio/images/buttons/toolbar-run.png), Android Studio builds and launches an app that displays the text "Hello from C++" on your Android device or emulator. The following overview describes the events that occur in order to build and run the sample app:

1.  Gradle calls upon your external build script, `CMakeLists.txt`.
2.  CMake follows commands in the build script to compile a C++ source file, `native-lib.cpp`, into a shared object library and names it `libnative-lib.so`, which Gradle then packages into the APK.
3.  During runtime, the app's `MainActivity` loads the native library using [`System.loadLibrary()`](https://developer.android.com/reference/java/lang/System#loadLibrary(java.lang.String)). The library’s native function, `stringFromJNI()`, is now available to the app.
4.  `MainActivity.onCreate()` calls `stringFromJNI()`, which returns "Hello from C++", and uses it to update the [`TextView`](https://developer.android.com/reference/android/widget/TextView).

**Note:** [Instant Run](https://developer.android.com/studio/run#instant-run) is not compatible with components of your project written in native code.

If you want to verify that Gradle packages the native library in the APK, you can use the [APK Analyzer](https://developer.android.com/studio/build/apk-analyzer):

1.  Select **Build > Build Bundles(s) / APK(s) > Build APK(s)**
2.  Select **Build > Analyze APK**.
3.  Select the APK from the `app/build/outputs/apk/` directory and click **OK**.
4.  As shown in figure 3, you can see `libnative-lib.so` in the APK Analyzer window under `lib/<ABI>/`. ![](https://developer.android.com/studio/images/projects/cpplib-apk-analyzer_2-2_2x.png)

    **Figure 3.** Locating a native library using the APK Analyzer.

**Tip:** If you want to experiment with other Android apps that use native code, click **File > New > Import Sample** and select a sample project from the **Ndk** list.

## Create new C/C++ source files

To add new C/C++ source files to an existing project, proceed as follows:

1.  If you don't already have a `cpp/` directory in the main source set of your app, create one as follows:

1.  Open the **Project** pane from the left side of the IDE and select the **Project** view from the drop\-down menu.
2.  Navigate to **your\-module > src**, right\-click on the **main** directory, and select **New > Directory**.
3.  Enter `cpp` as the directory name and click **OK**.

4.  Right\-click on the `cpp/` directory and select **New > C/C++ Source File**.

7.  Enter a name for your source file, such as `native-lib`.

10.  From the **Type** drop\-down menu, select the file extension for your source file, such as `.cpp`.
    *   You can add other file types to the drop\-down menu, such as `.cxx` or `.hxx`, by clicking **Edit File Types** ![](https://developer.android.com/studio/images/buttons/dialog-wrench.png). In the **C/C++** dialog box that pops up, select another file extension from the **Source Extension** and **Header Extension** drop\-down menus and click **OK**.

13.  If you also want to create a header file, check the **Create an associated header** checkbox.

16.  Click **OK**.

After you add new C/C++ files to you project, you still need to [configure CMake](https://developer.android.com/studio/projects/configure-cmake) to include them in your native library.

## Additional resources

To learn more about supporting C/C++ code in your app, try the following resource.

### Codelabs

*   [Create Hello\-CMake with Android Studio](https://codelabs.developers.google.com/codelabs/android-studio-cmake/), a codelab that shows you how to use the Android Studio CMake template to start Android NDK project development