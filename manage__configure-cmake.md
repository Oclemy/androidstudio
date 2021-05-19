# Configure CMake

*   [Table of contents](#top_of_page)
*   [Create a CMake build script](#create_script)
*   [Add NDK APIs](#add-ndk-api)
*   [Add other prebuilt libraries](#add-other-library)
*   [Include other CMake projects](#include-other-cmake-projects)
*   [Call CMake from the command line](#call-cmake-cli)

A CMake build script is a plain text file that you must name `CMakeLists.txt` and includes commands CMake uses to build your C/C++ libraries. If your native sources don't already have a CMake build script, you need to create one yourself and include the appropriate CMake commands. To learn how to install CMake, see [Install and configure the NDK and CMake](https://developer.android.com/studio/projects/install-ndk).

This section covers some basic commands you should include in your build script in order to tell CMake which sources to use when creating your native library. To learn more, read the official documentation about [CMake commands](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html).

After you configure a new CMake build script, you need to [configure Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds) to include your CMake project as a build dependency, so that Gradle builds and packages your native library with your app's APK.

**Note:** If your project uses ndk\-build, you don’t need to create a CMake build script. You can simply [configure Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds) to include your existing native library project by providing a path to your [`Android.mk`](https://developer.android.com/ndk/guides/android_mk) file.

## Create a CMake build script

To create a plain text file that you can use as your CMake build script, proceed as follows:

1.  Open the **Project** pane from the left side of the IDE and select the **Project** view from the drop\-down menu.
2.  Right\-click on the root directory of **your\-module** and select **New > File**.

    **Note:** You can create the build script in any location you want. However, when configuring the build script, paths to your native source files and libraries are relative to the location of the build script.

3.  Enter "CMakeLists.txt" as the filename and click **OK**.

You can now configure your build script by adding CMake commands. To instruct CMake to create a native library from native source code, add the [`cmake_minimum_required()`](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html) and [`add_library()`](https://cmake.org/cmake/help/latest/command/add_library.html) commands to your build script:

\# Sets the minimum version of CMake required to build your native library.
# This ensures that a certain set of CMake features is available to
# your build.

cmake\_minimum\_required(VERSION 3.4.1)

# Specifies a library name, specifies whether the library is STATIC or
# SHARED, and provides relative paths to the source code. You can
# define multiple libraries by adding multiple add\_library() commands,
# and CMake builds them for you. When you build your app, Gradle
# automatically packages shared libraries with your APK.

add\_library( # Specifies the name of the library.
             native\-lib

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             src/main/cpp/native\-lib.cpp )

**Tip:** Similar to how you can tell CMake to create a native library from source files, you can use the [`add_executable()`](https://cmake.org/cmake/help/latest/command/add_executable.html) command to tell CMake to instead create an executable from those source files. However, building executables from your native sources is optional, and building native libraries to package into your APK satisfies most project requirements.

When you add a source file or library to your CMake build script using `add_library()`, Android Studio also shows associated header files in the **Project** view after you sync your project. However, in order for CMake to locate your header files during compile time, you need to add the [`include_directories()`](https://cmake.org/cmake/help/latest/command/include_directories.html) command to your CMake build script and specify the path to your headers:

add\_library(...)

# Specifies a path to native header files.
include\_directories(src/main/cpp/include/)

The convention CMake uses to name the file of your library is as follows:

> `liblibrary-name.so`

For example, if you specify "native\-lib" as the name of your shared library in the build script, CMake creates a file named `libnative-lib.so`. However, when loading this library in your Java or Kotlin code, use the name you specified in the CMake build script:

static  { System.loadLibrary("native\-lib");
}

**Note:** If you rename or remove a library in your CMake build script, you need to clean your project before Gradle applies the changes or removes the older version of the library from your APK. To clean your project, select **Build > Clean Project** from the menu bar.

Android Studio automatically adds the source files and headers to the **cpp** group in the **Project** pane. By using multiple `add_library()` commands, you can define additional libraries for CMake to build from other source files.

## Add NDK APIs

The Android NDK provides a set of native APIs and libraries that you may find useful. You can use any of these APIs by including [the NDK libraries](https://developer.android.com/ndk/guides/stable_apis) in your project’s `CMakeLists.txt` script file.

Prebuilt NDK libraries already exist on the Android platform, so you don’t need to build them or package them into your APK. Because the NDK libraries are already a part of CMake’s search path, you don’t even need to specify the location of the library in your local NDK installation—you only need to provide CMake with the name of the library you want to use and link it against your own native library.

Add the [`find_library()`](https://cmake.org/cmake/help/latest/command/find_library.html) command to your CMake build script to locate an NDK library and store its path as a variable. You use this variable to refer to the NDK library in other parts of the build script. The following sample locates the [Android\-specific log support library](https://developer.android.com/ndk/guides/stable_apis#a3) and stores its path in `log-lib`:

find\_library( # Defines the name of the path variable that stores the
              # location of the NDK library.
              log\-lib

              # Specifies the name of the NDK library that
              # CMake needs to locate.
              log )

In order for your native library to call functions in the `log` library, you need to link the libraries using the [`target_link_libraries()`](https://cmake.org/cmake/help/latest/command/target_link_libraries.html) command in your CMake build script:

find\_library(...)

# Links your native library against one or more other native libraries.
target\_link\_libraries( # Specifies the target library.
                       native\-lib

                       # Links the log library to the target library.
                       ${log\-lib} )

The NDK also includes some libraries as source code that you need to build and link to your native library. You can compile the source code into a native library by using the `add_library()` command in your CMake build script. To provide a path to your local NDK library, you can use the `ANDROID_NDK` path variable, which Android Studio automatically defines for you.

The following command tells CMake to build `android_native_app_glue.c`, which manages [`NativeActivity`](https://developer.android.com/reference/android/app/NativeActivity) lifecycle events and touch input, into a static library and links it to `native-lib`:

add\_library( app\-glue
             STATIC
             ${ANDROID\_NDK}/sources/android/native\_app\_glue/android\_native\_app\_glue.c )

# You need to link static libraries against your shared native library.
target\_link\_libraries( native\-lib app\-glue ${log\-lib} )

## Add other prebuilt libraries

Adding a prebuilt library is similar to specifying another native library for CMake to build. However, because the library is already built, you need to use the [`IMPORTED`](https://cmake.org/cmake/help/latest/prop_tgt/IMPORTED.html#prop_tgt:IMPORTED) flag to tell CMake that you only want to import the library into your project:

add\_library( imported\-lib
             SHARED
             IMPORTED )

You then need to specify the path to the library using the [`set_target_properties()`](https://cmake.org/cmake/help/latest/command/set_target_properties.html) command as shown below.

Some libraries provide separate packages for specific CPU architectures, or [Application Binary Interfaces (ABI)](https://developer.android.com/ndk/guides/abis), and organize them into separate directories. This approach helps libraries take advantage of certain CPU architectures while allowing you to use only the versions of the library you want. To add multiple ABI versions of a library to your CMake build script, without having to write multiple commands for each version of the library, you can use the `ANDROID_ABI` path variable. This variable uses a list of the default [ABIs that the NDK supports](https://developer.android.com/ndk/guides/abis#sa), or a filtered list of ABIs you [manually configure Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds#specify-abi) to use. For example:

add\_library(...)
set\_target\_properties( # Specifies the target library.
                       imported\-lib

                       # Specifies the parameter you want to define.
                       PROPERTIES IMPORTED\_LOCATION

                       # Provides the path to the library you want to import.
                       imported\-lib/src/${ANDROID\_ABI}/libimported\-lib.so )

For CMake to locate your header files during compile time, you need to use the `include_directories()` command and include the path to your header files:

include\_directories( imported\-lib/include/ )

**Note:** If you want to package a prebuilt library that is not a build\-time dependency—for example, when adding a prebuilt library that is a dependency of `imported-lib`, you do not need perform the following instructions to link the library.

To link the prebuilt library to your own native library, add it to the `target_link_libraries()` command in your CMake build script:

target\_link\_libraries( native\-lib imported\-lib app\-glue ${log\-lib} )

To package the prebuilt library into your APK, you need to [manually configure Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds#configure-gradle) with the `sourceSets` block to include the path to your `.so` file. After building your APK, you can verify which libraries Gradle packages into your APK by using the [APK Analyzer](https://developer.android.com/studio/build/apk-analyzer).

## Include other CMake projects

If you want to build multiple CMake projects and include their outputs in your Android project, you can use one `CMakeLists.txt` file as the top\-level CMake build script (which is the one you [link to Gradle](https://developer.android.com/studio/projects/gradle-external-native-builds)) and add additional CMake projects as dependencies of that build script. The following top\-level CMake build script uses the [`add_subdirectory()`](https://cmake.org/cmake/help/latest/command/add_subdirectory.html) command to specify another `CMakeLists.txt` file as a build dependency and then links against its output just as it would with any other prebuilt library.

\# Sets lib\_src\_DIR to the path of the target CMake project.
set( lib\_src\_DIR ../gmath )

# Sets lib\_build\_DIR to the path of the desired output directory.
set( lib\_build\_DIR ../gmath/outputs )
file(MAKE\_DIRECTORY ${lib\_build\_DIR})

# Adds the CMakeLists.txt file located in the specified directory
# as a build dependency.
add\_subdirectory( # Specifies the directory of the CMakeLists.txt file.
                  ${lib\_src\_DIR}

                  # Specifies the directory for the build outputs.
                  ${lib\_build\_DIR} )

# Adds the output of the additional CMake build as a prebuilt static
# library and names it lib\_gmath.
add\_library( lib\_gmath STATIC IMPORTED )
set\_target\_properties( lib\_gmath PROPERTIES IMPORTED\_LOCATION
                       ${lib\_build\_DIR}/${ANDROID\_ABI}/lib\_gmath.a )
include\_directories( ${lib\_src\_DIR}/include )

# Links the top\-level CMake build output against lib\_gmath.
target\_link\_libraries( native\-lib ... lib\_gmath )

## Call CMake from the command line

Use the following command to call CMake to generate a Ninja project outside of Android Studio:

```
cmake
-Hpath/to/cmakelists/folder
-Bpath/to/generated/ninja/project/debug/ABI
-DANDROID_ABI=ABI                               // For example, arm64-v8a
-DANDROID_PLATFORM=platform-version-string      // For example, android-16
-DANDROID_NDK=android-sdk/ndk/ndk-version
-DCMAKE_TOOLCHAIN_FILE=android-sdk/ndk/ndk-version/build/cmake/android.toolchain.cmake
-G Ninja

```

This command will generate the Ninja project that can be executed to create Android executable libraries (`.so` files). The `CMAKE_TOOLCHAIN_FILE` is required to use NDK's CMake support.

**Note:** Use the following optional parameter if CMake is unable to locate the Ninja executable:
`-DCMAKE_MAKE_PROGRAM=path/to/ninja/ninja.exe`.