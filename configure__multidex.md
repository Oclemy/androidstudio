# Enable multidex for apps with over 64K methods

- [Enable multidex for apps with over 64K methods](#enable-multidex-for-apps-with-over-64k-methods)
  - [About the 64K reference limit](#about-the-64k-reference-limit)
    - [Multidex support prior to Android 5.0](#multidex-support-prior-to-android-50)
    - [Multidex support for Android 5.0 and higher](#multidex-support-for-android-50-and-higher)
  - [Avoid the 64K limit](#avoid-the-64k-limit)
  - [Configure your app for multidex](#configure-your-app-for-multidex)
    - [Limitations of the multidex library](#limitations-of-the-multidex-library)
  - [Declare classes required in the primary DEX file](#declare-classes-required-in-the-primary-dex-file)
    - [multiDexKeepFile property](#multidexkeepfile-property)
    - [multiDexKeepProguard property](#multidexkeepproguard-property)
  - [Optimize multidex in development builds](#optimize-multidex-in-development-builds)
  - [Test multidex apps](#test-multidex-apps)

When your app and the libraries it references exceed 65,536 methods, you encounter a build error that indicates your app has reached the limit of the Android build architecture:

trouble writing output:
Too many field references: 131000; max is 65536.
You may try using \-\-multi\-dex option.

Older versions of the build system report a different error, which is an indication of the same problem:

Conversion to Dalvik format failed:
Unable to execute dex: method ID not in \[0, 0xffff\]: 65536

Both these error conditions display a common number: 65536. This number represents the total number of references that can be invoked by the code within a single Dalvik Executable (DEX) bytecode file. This page explains how to move past this limitation by enabling an app configuration known as *multidex*, which allows your app to build and read multiple DEX files.

## About the 64K reference limit

Android app (APK) files contain executable bytecode files in the form of [Dalvik](https://source.android.com/devices/tech/dalvik/) Executable (DEX) files, which contain the compiled code used to run your app. The Dalvik Executable specification limits the total number of methods that can be referenced within a single DEX file to 65,536—including Android framework methods, library methods, and methods in your own code. In the context of computer science, the term [*Kilo, K*](https://en.wikipedia.org/wiki/Kilo-), denotes 1024 (or 2^10). Because 65,536 is equal to 64 X 1024, this limit is referred to as the '64K reference limit'.

### Multidex support prior to Android 5.0

Versions of the platform prior to Android 5.0 (API level 21) use the Dalvik runtime for executing app code. By default, Dalvik limits apps to a single `classes.dex` bytecode file per APK. In order to get around this limitation, you can add the multidex library to the module\-level `build.gradle` file:

dependencies { def multidex\_version \=  "2.0.1"implementation "androidx.multidex:multidex:$multidex\_version"
}

To view the current versions for this library, see the information about Multidex on the [versions](https://developer.android.com/jetpack/androidx/versions) page.

If you aren't using AndroidX, add the following deprecated support library dependency instead:

dependencies {implementation 'com.android.support:multidex:1.0.3'
}

This library becomes part of the primary DEX file of your app and then manages access to the additional DEX files and the code they contain. More details are below in the section about how to [configure your app for multidex](#mdex-gradle).

### Multidex support for Android 5.0 and higher

Android 5.0 (API level 21) and higher uses a runtime called ART which natively supports loading multiple DEX files from APK files. ART performs pre\-compilation at app install time which scans for `classesN.dex` files and compiles them into a single `.oat` file for execution by the Android device. Therefore, if your `minSdkVersion` is 21 or higher multidex is enabled by default, and you do not need the multidex library.

For more information on the Android 5.0 runtime, read [ART and Dalvik](https://source.android.com/devices/tech/dalvik/).

**Note:** When running your app using Android Studio, the build is optimized for the target devices you deploy to. This includes enabling multidex when the target devices are running Android 5.0 and above. Because this optimization is applied only when deploying your app using Android Studio, you might still need to [configure your release build](#mdex-gradle) for multidex to avoid the 64K limit.

## Avoid the 64K limit

Before configuring your app to enable use of 64K or more method references, you should take steps to reduce the total number of references called by your app code, including methods defined by your app code or included libraries. The following strategies can help you avoid hitting the DEX reference limit:

*   **Review your app's direct and transitive dependencies** \- Ensure any large library dependency you include in your app is used in a manner that outweighs the amount of code being added to the app. A common anti\-pattern is to include a very large library because a few utility methods were useful. Reducing your app code dependencies can often help you avoid the DEX reference limit.
*   **Remove unused code with R8** \- [Enable code shrinking](https://developer.android.com/studio/build/shrink-code) to run R8 for your release builds. Enabling shrinking ensures you are not shipping unused code with your APKs.

Using these techniques might help you avoid the need to enable multidex in your app while also decreasing the overall size of your APK.

## Configure your app for multidex

If your `minSdkVersion` is set to 21 or higher, multidex is enabled by default and you do not need the multidex library.

However, if your `minSdkVersion` is set to 20 or lower, then you must use the [multidex library](https://developer.android.com/jetpack/androidx/releases/multidex) and make the following modifications to your app project:

1.  Modify the module\-level `build.gradle` file to enable multidex and add the multidex library as a dependency, as shown here:

    android {defaultConfig { ...minSdkVersion 15 targetSdkVersion 28 **multiDexEnabled true** } ...
    }dependencies { **implementation "androidx.multidex:multidex:2.0.1"**
    }

2.  Depending on whether you override the [`Application`](https://developer.android.com/reference/android/app/Application) class, perform one of the following:
    *   If you *do not* override the [`Application`](https://developer.android.com/reference/android/app/Application) class, edit your manifest file to set `android:name` in the `<application>` tag as follows:

        <?xml version\="1.0" encoding\="utf\-8"?>
        <manifest  xmlns:android\="http://schemas.android.com/apk/res/android" package\="com.example.myapp"\> <application **android:name\="androidx.multidex.MultiDexApplication"**  \>... </application>
        </manifest>

    *   If you do override the [`Application`](https://developer.android.com/reference/android/app/Application) class, change it to extend `MultiDexApplication` (if possible) as follows:

        [Kotlin](#kotlin) [Java](#java) [More](#)

        class  MyApplication  :  MultiDexApplication()  {...}

        public  class  MyApplication  extends  MultiDexApplication  {  ...  }

    *   Or if you do override the [`Application`](https://developer.android.com/reference/android/app/Application) class but it's not possible to change the base class, then you can instead override the [`attachBaseContext()`](https://developer.android.com/reference/android/content/ContextWrapper#attachBaseContext(android.content.Context)) method and call`MultiDex.install(this)` to enable multidex:

        [Kotlin](#kotlin) [Java](#java) [More](#)

        class  MyApplication  :  SomeOtherApplication()  { override  fun attachBaseContext(base:  Context)  { super.attachBaseContext(base) MultiDex.install(this) }
        }

        public  class  MyApplication  extends  SomeOtherApplication  { @Override protected  void attachBaseContext(Context base)  {
             super.attachBaseContext(base);
             MultiDex.install(this); }
        }

        **Caution:** Do not execute `MultiDex.install()` or any other code through reflection or JNI before `MultiDex.install()` is complete. Multidex tracing will not follow those calls, causing `ClassNotFoundException` or verify errors due to a bad class partition between DEX files.

Now when you build your app, the Android build tools construct a primary DEX file (`classes.dex`) and supporting DEX files ( `classes2.dex`, `classes3.dex`, and so on) as needed. The build system then packages all DEX files into your APK.

At runtime, the multidex APIs use a special class loader to search all of the available DEX files for your methods (instead of searching only in the main `classes.dex` file).

### Limitations of the multidex library

The multidex library has some known limitations that you should be aware of and test for when you incorporate it into your app build configuration:

*   The installation of DEX files during startup onto a device's data partition is complex and can result in Application Not Responding (ANR) errors if the secondary DEX files are large. To avoid this issue, [enable code shrinking](https://developer.android.com/studio/build/shrink-code) to minimize the size of DEX files and remove unused portions of code.
*   When running on versions prior to Android 5.0 (API level 21), using multidex is not enough to work around the linearalloc limit ([issue 78035](http://b.android.com/78035)). This limit was increased in Android 4.0 (API level 14), but that did not solve it completely. And on versions lower than Android 4.0, you might reach the linearalloc limit before reaching the DEX index limit. So if you are targeting API levels lower than 14, test thoroughly on those versions of the platform, because your app might have issues at startup or when particular groups of classes are loaded.

    [Code shrinking](https://developer.android.com/studio/build/shrink-code) can reduce or possibly eliminate these issues.

## Declare classes required in the primary DEX file

When building each DEX file for a multidex app, the build tools perform complex decision\-making to determine which classes are needed in the primary DEX file so that your app can start successfully. If any class that's required during startup is not provided in the primary DEX file, then your app crashes with the error `java.lang.NoClassDefFoundError`.

This shouldn't happen for code that's accessed directly from your app code because the build tools recognize those code paths, but it can happen when the code paths are less visible such as when a library you use has complex dependencies. For example, if the code uses introspection or invocation of Java methods from native code, then those classes might not be recognized as required in the primary DEX file.

So if you receive `java.lang.NoClassDefFoundError`, then you must manually specify these additional classes as required in the primary DEX file by declaring them with the [`multiDexKeepFile`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html#com.android.build.gradle.internal.dsl.BuildType:multiDexKeepFile) or the [`multiDexKeepProguard`](https://google.github.io/android-gradle-dsl/2.2/com.android.build.gradle.internal.dsl.BuildType.html#com.android.build.gradle.internal.dsl.BuildType:multiDexKeepProguard) property in your build type. If a class is matched in either the `multiDexKeepFile` or the `multiDexKeepProguard` file, then that class is added to the primary DEX file.

### multiDexKeepFile property

The file you specify in `multiDexKeepFile` should contain one class per line, in the format `com/example/MyClass.class`. For example, you can create a file called `multidex-config.txt` that looks like this:

com/example/MyClass.class
com/example/MyOtherClass.class

Then you can declare that file for a build type as follows:

android {buildTypes {release {
            multiDexKeepFile file('multidex\-config.txt') ... } }
}

Remember that Gradle reads paths relative to the `build.gradle` file, so the above example works if `multidex-config.txt` is in the same directory as the `build.gradle` file.

### multiDexKeepProguard property

The `multiDexKeepProguard` file uses the same format as Proguard and supports the entire Proguard grammar. For more information about Proguard format and grammar, see the [Keep Options](http://proguard.sourceforge.net/manual/usage.html#keepoptions) section in the Proguard manual.

The file you specify in `multiDexKeepProguard` should contain `-keep` options in any valid ProGuard syntax. For example, `-keep com.example.MyClass.class`. You can create a file called `multidex-config.pro` that looks like this:

\-keep class com.example.MyClass
\-keep class com.example.MyClassToo

If you want to specify all classes in a package, the file looks like this:

\-keep class com.example.\*\* { \*; } // All classes in the com.example package

Then you can declare that file for a build type as follows:

android {buildTypes {release {
            multiDexKeepProguard file('multidex\-config.pro') ... } }
}

## Optimize multidex in development builds

A multidex configuration requires significantly increased build processing time because the build system must make complex decisions about which classes must be included in the primary DEX file and which classes can be included in secondary DEX files. This means that incremental builds using multidex typically take longer and can potentially slow your development process.

To mitigate longer incremental build times, you should use *pre\-dexing* to reuse multidex output between builds. Pre\-dexing relies on an ART format available only on Android 5.0 (API level 21) and higher. **If you're using Android Studio 2.3 and higher**, the IDE automatically uses this feature when deploying your app to a device running Android 5.0 (API level 21) or higher.

**Tip:** [Android plugin for Gradle 3.0.0](https://developer.android.com/studio/releases/gradle-plugin#3-0-0) and higher include further improvements to optimize build speeds, such as per\-class dexing (so that only the classes that you modify are re\-dexed). In general, for the best development experience, you should always upgrade to the [latest version of Android Studio](https://developer.android.com/studio/releases) and the Android plugin.

However, **if you're running Gradle builds from the command line**, you need to set the `minSdkVersion` to 21 or higher to enable pre\-dexing. A helpful strategy to preserve settings for your production build is to create two versions of your app [using product flavors](https://developer.android.com/studio/build/build-variants#product-flavors): a development flavor and a release flavor with different values for `minSdkVersion`, as shown below.

android {defaultConfig { ...multiDexEnabled true // The default minimum API level you want to support.minSdkVersion 15 }productFlavors { // Includes settings you want to keep only while developing your app.dev { // Enables pre\-dexing for command line builds. When using // Android Studio 2.3 or higher, the IDE enables pre\-dexing // when deploying your app to a device running Android 5.0 // (API level 21) or higher—regardless of what you set for // minSdkVersion.minSdkVersion 21 }prod { // If you've configured the defaultConfig block for the production version of // your app, you can leave this block empty and Gradle uses configurations in // the defaultConfig block instead. You still need to include this flavor. // Otherwise, all variants use the "dev" flavor configurations. } }buildTypes {release {minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard\-android.txt'),
                                                 'proguard\-rules.pro' } }
}dependencies {implementation "androidx.multidex:multidex:2.0.1"
}

To learn more strategies to help improve build speeds (from either Android Studio or the command line), read [Optimize Your Build Speed](https://developer.android.com/studio/build/optimize-your-build). For more information about using build variants, see [Configure Build Variants](https://developer.android.com/studio/build/build-variants).

**Tip:** Now that you have different build variants for different multidex needs, you can also provide a different manifest file for each variant (so only the one for API level 20 and lower changes the `<application>` tag name), or create a different `Application` subclass for each variant (so only the one for API level 20 and lower extends the `MultiDexApplication` class or calls `MultiDex.install(this)`).

## Test multidex apps

When you write instrumentation tests for multidex apps, no additional configuration is required if you use a [`MonitoringInstrumentation`](https://developer.android.com/reference/androidx/test/runner/MonitoringInstrumentation) (or an [`AndroidJUnitRunner`](https://developer.android.com/reference/androidx/test/runner/AndroidJUnitRunner)) instrumentation. If you use another [`Instrumentation`](https://developer.android.com/reference/android/app/Instrumentation), then you must override its `onCreate()` method with the following code:

[Kotlin](#kotlin) [Java](#java) [More](#)

fun onCreate(arguments:  Bundle)  { MultiDex.install(targetContext) super.onCreate(arguments) ...
}

public  void onCreate(Bundle arguments)  { MultiDex.install(getTargetContext()); super.onCreate(arguments); ...
}

**Notes:**

*   Don't use [`MultiDexTestRunner`](https://developer.android.com/reference/com/android/test/runner/MultiDexTestRunner), which is deprecated; use [`AndroidJUnitRunner`](https://developer.android.com/reference/androidx/test/runner/AndroidJUnitRunner) instead.
