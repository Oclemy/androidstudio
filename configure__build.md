# Configure your build

- [Configure your build](#configure-your-build)
  - [The build process](#the-build-process)
  - [Custom build configurations](#custom-build-configurations)
  - [Build configuration files](#build-configuration-files)
    - [The Gradle settings file](#the-gradle-settings-file)
    - [The top\-level build file](#the-top-level-build-file)
      - [Configure project\-wide properties](#configure-project-wide-properties)
    - [The module\-level build file](#the-module-level-build-file)
    - [Gradle properties files](#gradle-properties-files)
    - [Remap the NDK to a shorter path (Windows only)](#remap-the-ndk-to-a-shorter-path-windows-only)
    - [Syncing project with Gradle files](#syncing-project-with-gradle-files)
    - [Source sets](#source-sets)

The Android build system compiles app resources and source code, and packages them into APKs that you can test, deploy, sign, and distribute. Android Studio uses [Gradle](http://www.gradle.org/), an advanced build toolkit, to automate and manage the build process, while allowing you to define flexible custom build configurations. Each build configuration can define its own set of code and resources, while reusing the parts common to all versions of your app. The Android plugin for Gradle works with the build toolkit to provide processes and configurable settings that are specific to building and testing Android applications.

Gradle and the Android plugin run independent of Android Studio. This means that you can build your Android apps from within Android Studio, the command line on your machine, or on machines where Android Studio is not installed (such as continuous integration servers). If you are not using Android Studio, you can learn how to [build and run your app from the command line](https://developer.android.com/studio/build/building-cmdline). The output of the build is the same whether you are building a project from the command line, on a remote machine, or using Android Studio.

**Note:** Because Gradle and the Android plugin run independently from Android Studio, you need to update the build tools separately. Read the release notes to learn how to [update Gradle and the Android plugin](https://developer.android.com/studio/releases/gradle-plugin#updating-plugin).

The flexibility of the Android build system enables you to perform custom build configurations without modifying your app's core source files. This section helps you understand how the Android build system works, and how it can help you customize and automate multiple build configurations. If you simply want to learn more about deploying your app, see [Building and Running from Android Studio](https://developer.android.com/studio/run). To start creating custom build configurations right away using Android Studio, see [Configuring Build Variants](https://developer.android.com/studio/build/build-variants).

## The build process

The build process involves many tools and processes that convert your project into an Android Application Package (APK). The build process is very flexible, so it's useful to understand some of what is happening under the hood.

![](https://developer.android.com/images/tools/studio/build-process_2x.png)

**Figure 1.** The build process of a typical Android app module.

The build process for a typical Android app module, as shown in figure 1, follows these general steps:

1.  The compilers convert your source code into DEX (Dalvik Executable) files, which include the bytecode that runs on Android devices, and everything else into compiled resources.
2.  The APK Packager combines the DEX files and compiled resources into a single APK. Before your app can be installed and deployed onto an Android device, however, the APK must be signed.
3.  The APK Packager signs your APK using either the debug or release keystore:
    1.  If you are building a debug version of your app, that is, an app you intend only for testing and profiling, the packager signs your app with the debug keystore. Android Studio automatically configures new projects with a debug keystore.
    2.  If you are building a release version of your app that you intend to release externally, the packager signs your app with the release keystore. To create a release keystore, read about [signing your app in Android Studio](https://developer.android.com/studio/publish/app-signing#studio).
4.  Before generating your final APK, the packager uses the [zipalign](https://developer.android.com/studio/command-line/zipalign) tool to optimize your app to use less memory when running on a device.

At the end of the build process, you have either a debug APK or release APK of your app that you can use to deploy, test, or release to external users.

## Custom build configurations

Gradle and the Android plugin help you configure the following aspects of your build:

*Build types*

Build types define certain properties that Gradle uses when building and packaging your app, and are typically configured for different stages of your development lifecycle. For example, the debug build type enables debug options and signs the APK with the debug key, while the release build type may shrink, obfuscate, and sign your APK with a release key for distribution. You must define at least one build type in order to build your app—Android Studio creates the debug and release build types by default. To start customizing packaging settings for your app, learn how to [Configure build types](https://developer.android.com/studio/build/build-variants#build-types).

*Product flavors*

Product flavors represent different versions of your app that you may release to users, such as free and paid versions of your app. You can customize product flavors to use different code and resources, while sharing and reusing the parts that are common to all versions of your app. Product flavors are optional and you must create them manually. To start creating different versions of your app, learn how to [Configure product flavors](https://developer.android.com/studio/build/build-variants#product-flavors).

*Build variants*

A build variant is a cross product of a build type and product flavor, and is the configuration Gradle uses to build your app. Using build variants, you can build the debug version of your product flavors during development, or signed release versions of your product flavors for distribution. Although you do not configure build variants directly, you do configure the build types and product flavors that form them. Creating additional build types or product flavors also creates additional build variants. To learn how to create and manage build variants, read the [Configure build variants](https://developer.android.com/studio/build/build-variants) overview.

*Manifest entries*

You can specify values for some properties of the manifest file in the build variant configuration. These build values override the existing values in the manifest file. This is useful if you want to generate multiple APKs for your modules where each of the apk files has a different application name, minimum SDK version, or target SDK version. When multiple manifests are present, Gradle [merges manifest settings](https://developer.android.com/studio/build/manifest-merge).

*Dependencies*

The build system manages project dependencies from your local filesystem and from remote repositories. This prevents you from having to manually search, download, and copy binary packages of your dependencies into your project directory. To find out more, see [Add Build Dependencies](https://developer.android.com/studio/build/dependencies).

*Signing*

The build system enables you to specify signing settings in the build configuration, and it can automatically sign your APKs during the build process. The build system signs the debug version with a default key and certificate using known credentials to avoid a password prompt at build time. The build system does not sign the release version unless you explicitly define a signing configuration for this build. If you do not have a release key, you can generate one as described in [Sign your app](https://developer.android.com/studio/publish/app-signing).

*Code and resource shrinking*

The build system enables you to specify a different ProGuard rules file for each build variant. When building your app, the build system applies the appropriate set of rules to [shrink your code and resources](https://developer.android.com/studio/build/shrink-code) using its built\-in shrinking tools, such as R8.

*Multiple APK support*

The build system enables you to automatically build different APKs that each contain only the code and resources needed for a specific screen density or Application Binary Interface (ABI). For more information see [Build multiple APKs](https://developer.android.com/studio/build/configure-apk-splits).

## Build configuration files

Creating custom build configurations requires you to make changes to one or more build configuration files, or `build.gradle` files. These plain text files use Domain Specific Language (DSL) to describe and manipulate the build logic using [Groovy](http://groovy-lang.org/), which is a dynamic language for the Java Virtual Machine (JVM). You don’t need to know Groovy to start configuring your build because the Android plugin for Gradle introduces most of the DSL elements you need. To learn more about the Android plugin DSL, read the [DSL reference documentation](https://google.github.io/android-gradle-dsl/current/index.html).

When starting a new project, Android Studio automatically creates some of these files for you, as shown in figure 2, and populates them based on *sensible defaults*.

![](https://developer.android.com/images/tools/studio/project-structure_2x.png)

**Figure 2.** The default project structure for an Android app module.

There are a few Gradle build configuration files that are a part of the standard project structure for an Android app. Before you can start configuring your build, it is important to understand the scope and purpose of each of these files, and the basic DSL elements they should define.

### The Gradle settings file

The `settings.gradle` file, located in the root project directory, tells Gradle which modules it should include when building your app. For most projects, the file is simple and only includes the following:

include ‘:app’

However, multi\-module projects need to specify each module that should go into the final build.

### The top\-level build file

The top\-level `build.gradle` file, located in the root project directory, defines build configurations that apply to all modules in your project. By default, the top\-level build file uses the `buildscript` block to define the Gradle *repositories* and *dependencies* that are common to all modules in the project. The following code sample describes the default settings and DSL elements you can find in the top\-level `build.gradle` after creating a new project.

/\*\*
 \* The buildscript block is where you configure the repositories and
 \* dependencies for Gradle itself—meaning, you should not include dependencies
 \* for your modules here. For example, this block includes the Android plugin for
 \* Gradle as a dependency because it provides the additional instructions Gradle
 \* needs to build Android app modules.
 \*/buildscript { /\*\*
     \* The repositories block configures the repositories Gradle uses to
     \* search or download the dependencies. Gradle pre\-configures support for remote
     \* repositories such as JCenter, Maven Central, and Ivy. You can also use local
     \* repositories or define your own remote repositories. The code below defines
     \* JCenter as the repository Gradle should use to look for its dependencies.
     \*
     \* New projects created using [Android Studio 3.0](https://developer.android.com/studio/releases#3-0-0) and higher also include
     \* [Google's Maven repository](https://developer.android.com/studio/build/dependencies#google-maven).
     \*/repositories {
        google()
        jcenter() } /\*\*
     \* The dependencies block configures the dependencies Gradle needs to use
     \* to build your project. The following line adds Android plugin for Gradle
     \* version 4.0.0 as a classpath dependency.
     \*/dependencies {classpath 'com.android.tools.build:gradle:4.0.0' }
}

/\*\*
 \* The allprojects block is where you configure the repositories and
 \* dependencies used by all modules in your project, such as third\-party plugins
 \* or libraries. However, you should configure module\-specific dependencies in
 \* each module\-level build.gradle file. For new projects, Android Studio
 \* includes JCenter and Google's Maven repository by default, but it does not
 \* configure any dependencies (unless you select a template that requires some).
 \*/allprojects {repositories {
       google()
       jcenter()
   }
}

#### Configure project\-wide properties

For Android projects that include multiple modules, it may be useful to define certain properties at the project level and share them across all the modules. You can do this by adding [extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties) to the `ext` block in the top\-level `build.gradle` file.

buildscript {...}allprojects {...}

// This block encapsulates custom properties and makes them available to all
// modules in the project.ext { // The following are only a few examples of the types of properties you can define.compileSdkVersion \=  28 // You can also create properties to specify versions for dependencies. // Having consistent versions between modules can avoid conflicts with behavior.supportLibVersion \=  "28.0.0" ...
}
...

To access these properties from a module in the same project, use the following syntax in the module's `build.gradle` file (you can learn more about this file in the section below).

android { // Use the following syntax to access properties you defined at the project level: // rootProject.ext.property\_name
  compileSdkVersion rootProject.ext.compileSdkVersion ...
}
...dependencies {implementation "com.android.support:appcompat\-v7:${rootProject.ext.supportLibVersion}" ...
}

**Note:** Although Gradle allows you to define project\-wide properties at the module level, you should avoid doing so because it causes the modules that share those properties to be coupled. [Module coupling](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:decoupled_projects) makes it more difficult to later export a module as a stand\-alone project and effectively prevents Gradle from utilizing [parallel project execution](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:parallel_execution) to speed up multi\-module builds.

### The module\-level build file

The module\-level `build.gradle` file, located in each `project/module/` directory, allows you to configure build settings for the specific module it is located in. Configuring these build settings allows you to provide custom packaging options, such as additional build types and product flavors, and override settings in the `main/` app manifest or top\-level `build.gradle` file.

This sample Android app module `build.gradle` file outlines some of the basic DSL elements and settings that you should know.

/\*\*
 \* The first line in the build configuration applies the Android plugin for
 \* Gradle to this build and makes the android block available to specify
 \* Android\-specific build options.
 \*/

apply plugin:  'com.android.application'

/\*\*
 \* The android block is where you configure all your Android\-specific
 \* build options.
 \*/android { /\*\*
   \* compileSdkVersion specifies the Android API level Gradle should use to
   \* compile your app. This means your app can use the API features included in
   \* this API level and lower.
   \*/compileSdkVersion 28 /\*\*
   \* buildToolsVersion specifies the version of the SDK build tools, command\-line
   \* utilities, and compiler that Gradle should use to build your app. You need to
   \* download the build tools using the SDK Manager.
   \*
   \* This property is optional because the plugin uses a recommended version of
   \* the build tools by default.
   \*/buildToolsVersion "29.0.2" /\*\*
   \* The defaultConfig block encapsulates default settings and entries for all
   \* build variants, and can override some attributes in main/AndroidManifest.xml
   \* dynamically from the build system. You can configure product flavors to override
   \* these values for different versions of your app.
   \*/defaultConfig { /\*\*
     \* applicationId uniquely identifies the package for publishing.
     \* However, your source code should still reference the package name
     \* defined by the package attribute in the main/AndroidManifest.xml file.
     \*/applicationId 'com.example.myapp' // Defines the minimum API level required to run the app.minSdkVersion 15 // Specifies the API level used to test the app.targetSdkVersion 28 // Defines the version number of your app.versionCode 1 // Defines a user\-friendly version name for your app.versionName "1.0" } /\*\*
   \* The buildTypes block is where you can configure multiple [build types](https://developer.android.com/studio/build/build-variants#build-types).
   \* By default, the build system defines two build types: debug and release. The
   \* debug build type is not explicitly shown in the default build configuration,
   \* but it includes debugging tools and is signed with the debug key. The release
   \* build type applies Proguard settings and is not signed by default.
   \*/buildTypes { /\*\*
     \* By default, Android Studio configures the release build type to enable code
     \* shrinking, using minifyEnabled, and specifies the default Proguard rules file.
     \*/release {minifyEnabled true  // Enables code shrinking for the release build type.
        proguardFiles getDefaultProguardFile('proguard\-android.txt'),  'proguard\-rules.pro' } } /\*\*
   \* The productFlavors block is where you can configure multiple [product flavors](https://developer.android.com/studio/build/build-variants#product-flavors).
   \* This allows you to create different versions of your app that can
   \* override the defaultConfig block with their own settings. Product flavors
   \* are optional, and the build system does not create them by default.
   \*
   \* This example creates a free and paid product flavor. Each product flavor
   \* then specifies its own application ID, so that they can exist on the Google
   \* Play Store, or an Android device, simultaneously.
   \*
   \* If you declare product flavors, you must also declare flavor dimensions
   \* and assign each flavor to a flavor dimension.
   \*/ [flavorDimensions](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html#com.android.build.gradle.AppExtension:flavorDimensions(java.lang.String[]))  "tier"productFlavors {free {dimension "tier"applicationId 'com.example.myapp.free' }paid {dimension "tier"applicationId 'com.example.myapp.paid' } } /\*\*
   \* The splits block is where you can configure different APK builds that
   \* each contain only code and resources for a supported screen density or
   \* ABI. You'll also need to configure your build so that each APK has a
   \* different versionCode.
   \*/splits { // Settings to build multiple APKs based on screen density.density { // Enable or disable building multiple APKs.enable false // Exclude these densities when building multiple APKs.exclude "ldpi",  "tvdpi",  "xxxhdpi",  "400dpi",  "560dpi" } }
}

/\*\*
 \* The dependencies block in the module\-level build configuration file
 \* specifies dependencies required to build only the module itself.
 \* To learn more, go to [Add build dependencies](https://developer.android.com/studio/build/dependencies).
 \*/dependencies {
    implementation project(":lib")implementation 'com.android.support:appcompat\-v7:28.0.0'
    implementation fileTree(dir:  'libs', include:  \['\*.jar'\])
}

### Gradle properties files

Gradle also includes two properties files, located in your root project directory, that you can use to specify settings for the Gradle build toolkit itself:

`gradle.properties`

This is where you can configure project\-wide Gradle settings, such as the Gradle daemon's maximum heap size. For more information, see [The Build Environment](https://docs.gradle.org/current/userguide/build_environment.html).

`local.properties`

Configures local environment properties for the build system, including the following:

*   `ndk.dir` \- Path to the NDK. This property has been deprecated. Any downloaded versions of the NDK will be installed in the `ndk` directory within the Android SDK directory.
*   `sdk.dir` \- Path to the SDK.
*   `cmake.dir` \- Path to CMake.
*   `ndk.symlinkdir` \- in Android Studio 3.5+, creates a symlink to the NDK that can be shorter than the installed NDK path.

### Remap the NDK to a shorter path (Windows only)

The most common issue with Windows long paths is that tools (such as `ld.exe`) in the installed NDK folder end up with very deep paths, but the tools don't support long paths well.

In `local.properties`, you can set the property `ndk.symlinkdir` to request that the Gradle plugin create a symlink to the NDK. The path of that symlink can be shorter than the existing NDK folder. For example, `ndk.symlinkdir = C:\` will result in the following symlink: `C:\ndk\19.0.5232133`

### Syncing project with Gradle files

When you make changes to the build configuration files in your project, Android Studio requires that you sync your project files so that it can import your build configuration changes and run some checks to make sure your configuration won't create build errors.

To sync your project files, click **Sync Now** in the notification bar that appears when you make a change, as shown in figure 3, or click **Sync Project** ![](https://developer.android.com/studio/images/buttons/toolbar-sync-gradle.png) from the menu bar. If Android Studio notices any errors with your configuration, for example, your source code uses API features that are only available in an API level higher than your `compileSdkVersion`, the **Messages** window appears to describe the issue.

![](https://developer.android.com/images/tools/as-gradlesync.png)

**Figure 3.** Syncing the project with build configuration files in Android Studio.

### Source sets

Android Studio logically groups source code and resources for each module into *source sets*. A module’s `main/` source set includes the code and resources used by all its build variants. Additional source set directories are optional, and Android Studio does not automatically create them for you when you configure new build variants. However, creating source sets, similar to `main/`, helps organize files and resources that Gradle should only use when building certain versions of your app:

`src/main/`

This source set includes code and resources common to all build variants.

`src/buildType/`

Create this source set to include code and resources only for a specific build type.

`src/productFlavor/`

Create this source set to include code and resources only for a specific product flavor.

**Note:** If you configure your build to [combine multiple product flavors](https://developer.android.com/studio/build/build-variants#flavor-dimensions), you can create source set directories for each *combination* of product flavors between the flavor dimensions: `src/productFlavor1ProductFlavor2/`

`src/productFlavorBuildType/`

Create this source set to include code and resources only for a specific build variant.

For example, to generate the "fullDebug" version of your app, the build system merges code, settings, and resources from following source sets:

*   `src/fullDebug/` (the build variant source set)
*   `src/debug/` (the build type source set)
*   `src/full/` (the product flavor source set)
*   `src/main/` (the main source set)

**Note:** When you create a new file or directory in Android Studio, using the **File > New** menu options, you can create it for a specific source set. The source sets you can choose from are based on your build configurations, and Android Studio automatically creates the required directories if they don't already exist.

If different source sets contain different versions of the same file, Gradle uses the following priority order when deciding which file to use (source sets on the left override the files and settings of source sets to the right):

> build variant > build type > product flavor > main source set > library dependencies

This allows Gradle to use files that are specific to the build variant you are trying to build while reusing activities, application logic, and resources that are common to other versions of your app. When [merging multiple manifests](https://developer.android.com/studio/build/manifest-merge), Gradle uses the same priority order, so each build variant can define different components or permissions in the final manifest. To learn more about creating custom source sets, go to [Create Source Sets for Build Variants](https://developer.android.com/studio/build/build-variants#sourcesets).
